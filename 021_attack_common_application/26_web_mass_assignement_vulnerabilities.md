# Web Mass Assignment Vulnerabilities

Web mass assignment vulnerability is a type of security vulnerability where attackers can modify the model attributes of an application through the parameters sent to the server.

Ruby on Rails is a web application framework that is vulnerable to this type of attack. 

Assuming we have a User model with the following attributes:
class User < ActiveRecord::Base
  attr_accessible :username, :email
end

Parameters to send to the server
{ "user" => { "username" => "hacker", "email" => "hacker@example.com", "admin" => true } }

## Exploiting Mass Assignment Vulnerability

Python code :

for i,j,k in cur.execute('select * from users where username=? and password=?',(username,password)):
  if k:
    session['user']=i
    return redirect("/home",code=302)
  else:
    return render_template('login.html',value='Account is pending for approval')

try:
  if request.form['confirmed']:
    cond=True
except:
      cond=False
with sqlite3.connect("database.db") as con:
  cur = con.cursor()
  cur.execute('select * from users where username=?',(username,))
  if cur.fetchone():
    return render_template('index.html',value='User exists!!')
  else:
    cur.execute('insert into users values(?,?,?)',(username,password,cond))
    con.commit()
    return render_template('index.html',value='Success!!')

- Using Burp Suite
- Capture the HTTP POST request to the /registe
- Set the parameters :
username=new&password=test&confirmed=test

## LAB

ssh root@10.129.205.15
!x4;EW[ZLwmDx?=w
cat /opt/asset-manager/app.py

		try:
			if request.form['active']:
				cond=True
		except:
				cond=False

--> active