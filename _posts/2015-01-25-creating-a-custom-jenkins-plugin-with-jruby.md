---
layout: post
title:  "Creating a Custom Jenkins Plugin with JRuby"
author: jay
tags: [ jenkins, jruby, plugins, vagrant, continuous deployment ]
image: assets/images/headers/jenkins.png
description: "Creating a Custom Jenkins Plugin with JRuby"
featured: false
hidden: false
comments: false
---

 <p>I have seen scenarios where I would like to not have Jenkins not execute builds under certain circumstances. One common example is with the&nbsp;<a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://wiki.jenkins-ci.org/display/JENKINS/Maven+Project+Plugin" target="_blank">Maven Project Plugin</a>, it will often update the .pom file and check it back into source control. When you have Jenkins jobs that poll source code repos for new commits, your builds will enter an endless cycle of triggering more builds.</p>
<p>It would be nice to have the ability to stop builds from executing when the commit contained a specified phrase. I searched for such a plugin with no luck, so I have created my own plugin (that I use in production):</p>
<p><a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://github.com/jaygrossman/jenkins-ignore-commit-plugin" target="_blank">https://github.com/jaygrossman/jenkins-ignore-commit-plugin</a></p>

<p>Since there wasn't much detail for creating custom plugins in Ruby that I could find, this blog post will walk through the process.</p>
<p><strong style="margin: 0px; padding: 0px;">Options for making Jenkins Plugins</strong></p>
<p>1) Maven (default)<br>
2) JRuby</p>
<p>Since <a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://wiki.jenkins-ci.org/display/JENKINS/Plugin+tutorial" target="_blank">setting up .pom files</a> always seems painful to me, I wanted to try the Ruby option.</p>
<p>I found this very light post from 2013 that showed a few examples and the jpi gem with not much explanation:<br>
<a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+plugin+development+in+Ruby" target="_blank">https://wiki.jenkins-ci.org/display/JENKINS/Jenkins+plugin+development+in+Ruby</a></p>

<p><strong style="margin: 0px; padding: 0px;">Setting up a JRuby Plugin Development Environment</strong></p>
<p>I like to do all my development in reproducible environments when possible, so I set up a vagrant environment for building and testing Jenkins Plugin development &amp; testing.</p>
<p>My <a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://github.com/jaygrossman/jenkins-ignore-commit-plugin/blob/master/Vagrantfile" target="_blank">Vagrantfile</a>&nbsp;installs the following dependencies on Centos 6.5:</p>
<ul>
<li>Java 1.6</li>
<li>Maven&nbsp;</li>
<li>Jenkins</li>
<li>rbenv &amp; jruby 1.7.9</li>
<li>jpi gem</li>
</ul>
<p><strong style="margin: 0px; padding: 0px;">Setting up a JRuby Plugin Development Project</strong></p>
<p>1) Create a directory with the name of your project (jenkins-ignore-commit-plugin)</p>

<p>2) Create a pluginspec file (jenkins-ignore-commit-plugin.pluginspec):</p>

    Jenkins::Plugin::Specification.newdo |plugin|
        plugin.name = "jenkins-ignore-commit-plugin"
        plugin.display_name = "Ignore Commit Plugin"
        plugin.version = '0.0.1'
        plugin.description = 'Commits that contain a supplied phrase within the 
        commit messages will be skipped.'
        
        # You should create a wiki-page for your plugin when you publish it, see
        # https://wiki.jenkins-ci.org/display/JENKINS/Hosting+Plugins#HostingPlugins-AddingaWikipage
        # This line makes sure it's listed in your POM.
        plugin.url = 'https://wiki.jenkins-ci.org/display/JENKINS/Ignore+Commit+Plugin'
        
        # The first argument is your user name for jenkins-ci.org.
        plugin.developed_by "jaygrossman", "Jay Grossman <jay.grossman@org>"
        
        plugin.uses_repository :github => "jaygrossman/jenkins-ignore-commit-plugin"
        # This is a required dependency for every ruby plugin.
        plugin.depends_on 'ruby-runtime', '0.12'
    end 

<p>3) You'll need these gems: jenkins-plugin-runtime, jpi, jruby-openssl. Here is my Gemfile:</p>

    gem "jenkins-plugin-runtime", "~> 0.2.3"
    
    group :development do
        gem "jpi", "~> 0.3.8"
        gem "jruby-openssl", "~> 0.8.8"
        gem "rake", "~> 10.0.4"
        gem "pry"
        gem 'coveralls', require: false
        gem 'rubyzip', "~> 0.9.9"
    end


<p>4) If your plugin requires user interface elements (such as checkbox, textbox, textarea, password) such as the items shown below, you'll want to create a View.</p>

<p><img src="{{ site.baseurl }}/assets/images/configure_jenkins_plugin.png" alt="configure_jenkins_plugin"/></p>

<p>To set up the View:</p>
<li>Create views sub-directory.</li>
<li>Create a plugin sub-directory with views (I called my directory views/ignore_commit).&nbsp;</li>
<li>Create a file named config.erb in that directory.</li>
<li>The entry() function can define the form element, title, field id, and description as shown below:</li>

    <% 
    f = taglib("/lib/form")
    f.entry(:title => 'phrase', :field => 'ignore_commit_phrase', 
    :description => "Commits containing this phrase will be considered NOT_BUILT") do
    f.textbox
    end
    %>


<p>5) Next you'll want to create a model to do the actual work for the plugin.</p>
<li>a) create a models sub-directory</li>
<li>b) create a file plugin.rb file (mine is called ignore_commit.rb):</li>


    class IgnoreCommit < Jenkins::Tasks::BuildWrapper
    display_name "Ignore Commits with Phrase"

    attr_accessor :ignore_commit_phrase

    def initialize(attrs)
        @ignore_commit_phrase = attrs['ignore_commit_phrase']
    end

    # Here we test if any of the changes warrant a build
    def setup(build, launcher, listener)
        begin
        changeset = build.native.getChangeSet()
        # XXX: Can there be files in the changeset if it's manually triggered?
        # If so, how do we check for manual trigger?
        if changeset.isEmptySet()
            listener.info "Empty changeset, running build."
            return
        end

        logs = changeset.getLogs()
        latest_commit = logs.get(logs.size - 1)
        comment = latest_commit.getComment()

        if comment.include? ignore_commit_phrase
            listener.info "Build is skipped through commit message."
            listener.info "Commit: #{latest_commit.getCommitId()}"
            listener.info "Message: #{comment}"
            
            build.native.setResult(Java.hudson.model.Result::NOT_BUILT)
            build.halt("Build is skipped by Ignore Commit Plugin.")
        end

        rescue
            listener.error "Encountered exception when scanning for filtered paths: #{$!}"
            listener.error "Allowing build by default."
            return
        end

        listener.error "Encountered exception when looking commit message: #{$!}"
        listener.error "Allowing build by default."
        end
    end

<p><strong>Building the code and generating the Jenkins Plugin</strong></p>
<p>From within the project directory, run:</p>

    jpi build

<p>The JPI gem executes a build via maven and will compile a .hpi file (plugin binary file that can be uploaded into Jenkins) in a pkg sub-directory:</p>

<p><img src="{{ site.baseurl }}/assets/images/hpi_file.png" alt="configure_jenkins_plugin"/></p>

<p>You can then manually install the plugin from the Plugin Manager upload page in Jenkins.</p>
<p>If you have a local&nbsp;&nbsp;local Jenkins environment (like in our vagrant set up), you can run the following commands to upload the plugin to it:</p>

    bundle update rake
    jpi server

<p><b>Running the Build and Testing Vagrant image</b></p>

<p> 1) Download this Vagrantfile and put it in the root of your plugin project directory:</p>

<p><a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://github.com/jaygrossman/jenkins-ignore-commit-plugin/blob/master/Vagrantfile" target="_blank">https://github.com/jaygrossman/jenkins-ignore-commit-plugin/blob/master/Vagrantfile</a></p>

<p> 2) Run this command to build the plugin and load it into a local Jenkins instance:</p>
 
    vagrant up

<p> 3) Once the vagrant up execution is complete, paste the following link into a web browser on the host machine to view the Jenkins instance running in the vagrant:</p>

    http://localhost:58080


