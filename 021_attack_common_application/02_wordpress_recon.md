# WordPress - Discovery & Enumeration

Open-source Content Management System (CMS)
SEO friendly

WordPress is written in PHP and usually runs on Apache with MySQL as the backend.

## Discovery/Footprinting

### Browsing to the /robots.txt file

User-agent: *
Disallow: /wp-admin/
Allow: /wp-admin/admin-ajax.php
Disallow: /wp-content/uploads/wpforms/

Sitemap: https://inlanefreight.local/wp-sitemap.xml

### Navigating to http://blog.inlanefreight.local/wp-login.php

5 types of users on a standard WordPress installation.

1. Administrator: This user has access to administrative features within the website. This includes adding and deleting users and posts, as well as editing source code.
2. Editor: An editor can publish and manage posts, including the posts of other users.
3. Author: They can publish and manage their own posts.
4. Contributor: These users can write and manage their own posts but cannot publish them.
5. Subscriber: These are standard users who can browse posts and edit their profiles.

## Enumeration

curl -s http://blog.inlanefreight.local | grep WordPress
<meta name="generator" content="WordPress 5.8" /

curl -s http://blog.inlanefreight.local/ | grep themes
<link rel='stylesheet' id='bootstrap-css'  href='http://blog.inlanefreight.local/wp-content/themes/business-gravity/assets/vendors/bootstrap/css/bootstrap.min.css' type='text/css' media='all' />

curl -s http://blog.inlanefreight.local/ | grep plugins
<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/subscriber.js?ver=5.8' id='subscriber-js-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine-en.js?ver=5.8' id='validation-engine-en-js'></script>
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/jquery.validationEngine.js?ver=5.8' id='validation-engine-js'></script>
		<link rel='stylesheet' id='mm_frontend-css'  href='http://blog.inlanefreight.local/wp-content/plugins/mail-masta/lib/css/mm_frontend.css?ver=5.8' type='text/css' media='all' />
<script type='text/javascript' src='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/js/index.js?ver=5.4.2' id='contact-form-7-js'></script>

Browsing to http://blog.inlanefreight.local/wp-content/plugins/mail-masta/ :
Shows us that directory listing is enabled and that a readme.txt file is present
--> version 1.0.0 of the plugin is installed, which suffers from a Local File Inclusion vulnerability

curl -s http://blog.inlanefreight.local/?p=1 | grep plugins
<link rel='stylesheet' id='contact-form-7-css'  href='http://blog.inlanefreight.local/wp-content/plugins/contact-form-7/includes/css/styles.css?ver=5.4.2' type='text/css' media='all' />
<link rel='stylesheet' id='wpdiscuz-frontend-css-css'  href='http://blog.inlanefreight.local/wp-content/plugins/wpdiscuz/themes/default/style.css?ver=7.0.4' type='text/css' media='all' />

--> This plugin version shows this unauthenticated remote code execution vulnerability from June of 2021 :
https://www.exploit-db.com/exploits/49967

## Enumerating Users

http://blog.inlanefreight.local/wp-login.php

--> Invalid usernames return that the user is not found (user admin OK) :
WordPress vulnerable to username enumeration, which can be used to obtain a list of potential usernames

## WPScan

sudo gem install wpscan
sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token dEOFB

## LAB

sudo gem install wpscan
sudo wpscan --url http://blog.inlanefreight.local --enumerate --api-token BNZba2aykBraNiv7xYyWKxQmj5wRppDgiHs23AAMaHA

http://blog.inlanefreight.local/wp-content/uploads/2021/08/flag.txt

<p><a href="http://wordpress.org/plugins/wp-sitemap-page/">Powered by "WP Sitemap Page"</a></p></div></strong></p>

wp sitemap page

curl -s http://blog.inlanefreight.local/?p=1 | grep wp-sitemap-page

--> Failed

http://blog.inlanefreight.local/wp-content/plugins/wp-sitemap-page/readme.txt

Tested up to: 5.6.2
Stable tag: 1.6.4