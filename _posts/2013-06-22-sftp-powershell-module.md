---
layout: post
title:  "Powershell Module for transferring files via SFTP"
author: jay
categories: [ analysis, business ]
tags: [  powershell, module, sftp, winscp  ]
image: assets/images/headers/ftp.png
description: "Powershell Module for transferring files via SFTP"
featured: false
hidden: false
comments: false
#rating: 4.5
---


<p>I had the need for several automated jobs to be able to exchange files securely with partners via the Secure FTP (SFTP).&nbsp;I chose powershell because the jobs:</p>
<ol>
<li >get executed on windows nodes. It is less overhead than creating console apps in .NET</li>
<li >required things like querying databases, transforming data, and archiving files</li>
</ol>
<p>I spent a little bit of time looking into different options, and decided to utilize the latest version of the&nbsp;<a  href="http://winscp.net/eng/index.php" target="_blank">WinSCP</a>&nbsp;library provided the required options - such as designating the transfer mode, deleting source files, and specifying file masks.</p>
<p >I created a generic powershell module that exposes upload and download functions. The module is available on github:</p>
<p><a style="margin: 0px; padding: 0px; text-decoration: none; color: #1fa2e1;" href="https://github.com/jaygrossman/SftpPowershellModule" target="_blank">https://github.com/jaygrossman/SftpPowershellModule</a></p>


    Function SFTPDownloadFiles {
      Param(

            [string] $Username, # Required. Username of SFTP account
            [string] $Password, # Required. Password of SFTP account
            [string] $HostName, # Required. HostName of SFTP server
            [string] $RemotePath, # Required. Starting path of SFTP server
            [string] $LocalPath, # Required. Starting path of local server
            [string] $SshHostKeyFingerprint, # Required. Host Key, 
            ssh-rsa 1024 xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
            [string] $FileMask, # Optional. Filter string to limit files to download
            [bool]   $Remove=$false, # Optional. Whether to remove original file
            [string] $TransferMode="binary"# Optional. Transfer can be binary, ascii, or automatic
      ) 

    Function SFTPUploadFiles {
        Param(
                [string] $Username, # Required. Username of SFTP account
                [string] $Password, # Required. Password of SFTP account
                [string] $HostName, # Required. HostName of SFTP server
                [string] $RemotePath, # Required. Starting path of SFTP server
                [string] $LocalPath, # Required. Starting path of local server
                [string] $SshHostKeyFingerprint, # Required. Host Key, 
                ssh-rsa 1024 xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx:xx
                [string] $FileMask, # Optional. Filter string to limit files to upload
                [bool]   $Remove=$false, # Optional. Whether to remove original file
                [string] $TransferMode="binary"# Optional. Transfer can be binary, ascii, or automatic
        )
    } 