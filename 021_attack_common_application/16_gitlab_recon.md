# Gitlab - Discovery & Enumeration

It is open-source and originally written in Ruby, but the current technology stack includes Go, Ruby on Rails, and Vue.js.

Comparison between GitHub and BitBucket : https://stackshare.io/stackups/bitbucket-vs-github-vs-gitlab

http://gitlab.inlanefreight.local:8081/admin/application_settings/general

## Footprinting & Discovery

http://gitlab.inlanefreight.local:8081/users/sign_in

The only way to footprint the GitLab version number in use is by browsing to the /help page when logged in

## Enumeration

http://gitlab.inlanefreight.local:8081/explore

Check if there are any public projects that may contain something interesting

Try to find :
- hard-coded credentials
- a script or configuration file containing credentials
- other secrets such as an SSH private key or API key

From here, we can explore each of the pages linked in the top left groups, snippets, and help

We can also use the registration form to enumerate valid users

--> Username is already taken

## LAB

IP=10.129.201.88
printf "%s\t%s\n\n" "$IP" "gitlab.inlanefreight.local" | sudo tee -a /etc/hosts

--> Register account
roott
Welcome123

http://gitlab.inlanefreight.local:8081/help

13.10.2

http://gitlab.inlanefreight.local:8081/search?utf8=%E2%9C%93&search=postgre&group_id=&project_id=2&scope=&search_code=true&snippets=false&repository_ref=master&nav_source=navbar

http://gitlab.inlanefreight.local:8081/root/inlanefreight-dev/-/blob/master/phpunit_pgsql.xml

<phpunit bootstrap="Tests/bootstrap.php" colors="true">
    <php>
        <var name="db_dsn" value="pgsql:dbname=hello_world_test;host=postgres"/>
        <var name="db_username" value="postgres"/>
        <var name="db_password" value="postgres"/>
    </php>
