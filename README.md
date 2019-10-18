# wordpress-apis



# get wordpress pages
# api paths

# http://localhost/wordpress/index.php/wp-json/wp/v2/pages

# http://localhost/wordpress/index.php/wp-json/wp/v2/post


```
        <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.11.3/jquery.min.js"></script>
        <script type="text/javascript">
          $('document').ready(function(){
            $('#clkbtn').click(function (e) {

           e.preventDefault(); //prevent default form gh

             $.ajax({
                 type: "GET",
                 url: 'http://localhost/wordpress/index.php/wp-json/wp/v2/pages',
                 dataType: 'json',
                 error: function(a,b,c) {
                  $("#signin-response").html("Unable to process request");
                  $("#signin-response").addClass("alert alert-error text-center");
                 },
                 cache: false,
                 success: function(data){

                    var json = JSON.parse(JSON.stringify(data));
                    $(json).each(function (i, val) {
                    //console.log(i + " : " + val);
                      $.each(val, function(k, v) {
                                    console.log(v.rendered);
                                    
                                    var jsonn = JSON.parse(JSON.stringify(v));
                                    //console.log(jsonn.id);
                                    console.log(k + " : " + v);
                                    if (k != 'guid') {
                                        if (k == "title") {
                                            jQuery('#dispclass').append("<h1>" + v.rendered + "</h1>");
                                        } else {
                                            jQuery('#dispclass').append(v.rendered);

                                        }

                                    } else {

                                    }


                                    //console.log(v);
                                    //jQuery('#dispclass').append(v.rendered);
                                });

                    }); 

                 }
               });
           }); 
           });
        </script>
```

# Register new user

```
add_action('rest_api_init', 'wp_rest_user_endpoints');
/**
 * Register a new user
 *
 * @param  WP_REST_Request $request Full details about the request.
 * @return array $args.
 **/
function wp_rest_user_endpoints($request) {
  /**
   * Handle Register User request.
   */
  register_rest_route('wp/v2', 'users/register', array(
    'methods' => 'POST',
    'callback' => 'wc_rest_user_endpoint_handler',
  ));
}
function wc_rest_user_endpoint_handler($request = null) {
  $response = array();
  $parameters = $request->get_json_params();
  $username = sanitize_text_field($parameters['username']);
  $email = sanitize_text_field($parameters['email']);
  $password = sanitize_text_field($parameters['password']);
  // $role = sanitize_text_field($parameters['role']);
  $error = new WP_Error();
  if (empty($username)) {
    $error->add(400, __("Username field 'username' is required.", 'wp-rest-user'), array('status' => 400));
    return $error;
  }
  if (empty($email)) {
    $error->add(401, __("Email field 'email' is required.", 'wp-rest-user'), array('status' => 400));
    return $error;
  }
  if (empty($password)) {
    $error->add(404, __("Password field 'password' is required.", 'wp-rest-user'), array('status' => 400));
    return $error;
  }
  // if (empty($role)) {
  //  $role = 'subscriber';
  // } else {
  //     if ($GLOBALS['wp_roles']->is_role($role)) {
  //      // Silence is gold
  //     } else {
  //    $error->add(405, __("Role field 'role' is not a valid. Check your User Roles from Dashboard.", 'wp_rest_user'), array('status' => 400));
  //    return $error;
  //     }
  // }
  $user_id = username_exists($username);
  if (!$user_id && email_exists($email) == false) {
    $user_id = wp_create_user($username, $password, $email);
    if (!is_wp_error($user_id)) {
      // Ger User Meta Data (Sensitive, Password included. DO NOT pass to front end.)
      $user = get_user_by('id', $user_id);
      // $user->set_role($role);
      $user->set_role('subscriber');
      // WooCommerce specific code
      if (class_exists('WooCommerce')) {
        $user->set_role('customer');
      }
      // Ger User Data (Non-Sensitive, Pass to front end.)
      $response['code'] = 200;
      $response['message'] = __("User '" . $username . "' Registration was Successful", "wp-rest-user");
    } else {
      return $user_id;
    }
  } else {
    $error->add(406, __("Email already exists, please try 'Reset Password'", 'wp-rest-user'), array('status' => 400));
    return $error;
  }
  return new WP_REST_Response($response, 123);
}
```
