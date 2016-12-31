Quick and dirty bounce handler

**Usage**

> `require_once('bounce_driver.class.php'); $bouncehandler=new Bouncehandler(); $multiArray=$bouncehandler->get_the_facts(\$strEmail)`

( $multiArray=$bouncehandler->get_the_facts(\$strEmail) can be replaced by $multiArray = $bouncehandler->parse_email($strEmail) )

Returns a multi-dimensional associative array of bounced recipient addresses and their SMTP status codes (if available) - see `print_r(\$multiArray);`

Will return recipient's email address, the RFC1893 error code, and the action. Action can be one of the following:

<dl>

<dt>transient</dt>

<dd>Temporary problem</dd>

<dt>failed</dt>

<dd>Permanent problem</dd>

<dt>autoresponse</dt>

<dd>Vacation auto-response/auto-responder</dd>

<dt>""</dt>

<dd>Empty string - not classified</dd>

</dl>

You could dereference the \$multiArray in a 'for loop', for example...

<pre>    foreach($multiArray as $the){
        switch($the['action']){
            case 'failed':
                //do something
                kill_him($the['recipient']);
                break;
            case 'transient':
                //do something else
                $num_attempts  = delivery_attempts($the['recipient']);
                if($num_attempts  > 10){
                    kill_him($the['recipient']);
                }
                else{
                    insert_into_queue($the['recipient'], ($num_attempts+1));
                }
                break;
            case 'autoresponse':
                //do something different
                postpone($the['recipient'], '7 days');
                break;
            default:
                //don't do anything
                break;
        }
    }
</pre>

Or, if it is an FBL, you could use the class variables to access the data (Unlike Multipart-reports, FBL's report only one bounce) You could also use them if the output array has only one index:

<pre>    if($bouncehandler->type == 'fbl'
       or count($bouncehandler->output) == 1)
    {
        echo $bouncehandler->action;
        echo $bouncehandler->status;
        echo $bouncehandler->recipient;
        echo $bouncehandler->feedback_type;
    }
</pre>

These class variables are safe for all bounce types:

*   $bouncehandler->type
*   $bouncehandler->subject
*   $bouncehandler->web_beacon_1
*   $bouncehandler->web_beacon_2
*   $bouncehandler->x_header_beacon_1
*   $bouncehandler->x_header_beacon_2

That's all you need to know, but if you want to get more complicated you can. Most methods are public. See source code.
