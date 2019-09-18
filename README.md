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
                      $.each(val, function (k, v) {
                        console.log(k + " : " + v);
                       jQuery('#dispclass').append(v);

                      });

                    }); 

                 }
               });
           }); 
           });
        </script>
```
