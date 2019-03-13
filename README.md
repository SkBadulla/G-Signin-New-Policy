# Updating Login Page


**login.html Page**

(1) Include the following in <head>..</head> tag:

<meta name="google-signin-client_id" content="YOUR CLIENT ID HERE">
<script src="https://ajax.googleapis.com/ajax/libs/jquery/1.12.4/jquery.min.js"></script>
<script src="https://apis.google.com/js/client:platform.js?onload=start" async defer></script>
<script>
  function start() {
    gapi.load('auth2', function() {
      auth2 = gapi.auth2.init({
        client_id: 'YOUR CLIENT ID HERE'
      });
    });
  }
</script>


(2) In the <body> tag, add the below button and div


<div id="signinButton" style="padding: 5px;margin-top:10px;text-align: center;">
  <span class="g-signin2"
    data-scope="profile openid email"
    data-client_id="YOUR CLIENT ID HERE"
    data-redirecturi="postmessage"
    data-accesstype="offline"
    data-cookiepolicy="single_host_origin"
    data-callback="signInCallback"
    data-approvalprompt="force">
  </span>
</div>   

<div id="result"></div>

(3) Add the below script before the ending </body> tag


<script>
  $('#signinButton').click(function() {
    function signInCallback(authResult){
      if (authResult['code']){
        $('#signinButton').attr('style', 'display: none');
        $.ajax({
          type: 'POST',
          url: '/gconnect?state={{STATE}}',
          headers: {
          'X-Requested-With': 'XMLHttpRequest'
          },
          contentType: 'application/octet-stream; charset=utf-8',
          success:function(result){
            $('#result').html('Login Successful!</br>'+ result + '</br>Redirecting...')
                 setTimeout(function() {
                  window.location.href = "/";
                 }, 2000);
          },
          processData:false,
          data:authResult['code']
        });
      } else{
        // handle error
        console.log('There was an error: ' + authResult['error']);
        $('#result').html('Failed to make a server-side call. Check your configuration and console.');
      }
    }
    auth2.grantOfflineAccess().then(signInCallback);
  });
</script>
