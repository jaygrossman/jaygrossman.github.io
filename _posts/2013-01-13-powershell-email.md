---
layout: post
title:  "Sending email through Gmail SMTP server - C# and Powershell examples"
author: jay
tags: [ powershell ]
image: assets/images/headers/send_email.jpg
description: "Sending email through Gmail SMTP server - C# and Powershell examples"
featured: false
hidden: false
comments: false
---

<p>I know I haven't posted much code on this blog, but this snippet I found valuable.</p>
<p>While I can certainly host my SMTP server, it would be so much easier if I could just Google's (since I am using Google Apps for my site's email). Below is the code you can use to send email's via Google's SMTP server. enjoy...</p>

<p><b>C#</b></p>

    string GmailUserName="myusername@gmail.com";
    string GmailPassword="mypassword";
    string SendTo="sendto@gmail.com";
    string EmailSubject="Test Subject";
    string EmailBody="Test Body";

    System.Net.Mail.SmtpClient client = new System.Net.Mail.SmtpClient("smtp.gmail.com", 587);
    client.Credentials = new NetworkCredential(GmailUserName, GmailPassword);
    client.EnableSsl = true;
    client.Send(GmailUserName, SendTo, EmailSubject, EmailBody);

<p><b>Powershell</b></p>

    $emailSmtpServer="smtp.gmail.com"
    $emailSmtpServerPort="587"
    $emailSmtpUser="myusername@gmail.com"
    $emailSmtpPass="mypassword"
    $emailFrom="myusername <myusername@gmail.com>"
    $emailTo="sendto@gmail.com" 
    $emailMessage=New-Object System.Net.Mail.MailMessage( $emailFrom , $emailTo )
    $emailMessage.Subject="Test Subject"
    $emailMessage.IsBodyHtml=$true
    $emailMessage.Body=@"
    <p>Here is a message that is <strong>HTML formatted</strong>.</p>
    <p>From the SMTP script</p>
    "@
    $SMTPClient=New-Object System.Net.Mail.SmtpClient( $emailSmtpServer , $emailSmtpServerPort )
    $SMTPClient.EnableSsl=$true
    $SMTPClient.Credentials=New-Object System.Net.NetworkCredential( $emailSmtpUser , $emailSmtpPass );
    $SMTPClient.Send( $emailMessage )

<p>After this code sends the email, it will appear in the account's Gmail sent mail folder (just as if you had sent it via tthe gmail.com site).</p>
<p>Since Gmail does not have much of a mail merge option unless you use Google Docs, I wound up using this code for personalizing messages within an opt-in email newsletter.</p>
   