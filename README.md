# Login and Registration

A module for ProcessWire 3.x that provides a self contained process for 
rendering and processing login, user registration, and profile edits. 
New user registration requires the user’s email address for validation. 

For those that have more than basic needs, this moduel also serves as a 
good proof-of-concept for further development and integration. 

## Installation and setup

### First steps

Install the module as with any other module, by placing the included files
in /site/modules/LoginRegister/, go to “Modules > Refresh” in your admin,
and then click “install” for the this module. 

### Configure

You should now see a configuration screen for this module. This screen
will let you determine what features you want to use with it, and what
fields you want to allow for user registration and profile editing. 

By default, the email and password fields are required for both forms. You
may want to add more fields. To do this, you’ll need to add fields to 
your “user” template. You can add fields to your user template in the admin
by going to “Setup > Templates > Show system templates > user”. 

You should stick to using simple text-based fields for now. File/image 
fields, repeaters, rich text editors and combination fields fields are not 
supported on the front-end at present. 

### Output

After installation and configuration, either create a new template or use 
an existing template to use this module with. This module will provide the 
“content” or “body copy” area of your page, and that’s where you should 
use its output. Edit your template file and place the following in it:

**Direct output or markup regions:**
~~~~~
<?= $modules->get('LoginRegister')->execute() ?>
~~~~~
The above is for direct output. If you are instead using delayed output and
want to populate the module’s output to a variable, you might do it like this:

**Delayed output:**
~~~~~
$content = $modules->get('LoginRegister')->execute();
~~~~~

That single line above is all that you need, and the module will do the rest.

## How it works

If presented to a guest user (not logged in) it will display a login form 
and a registration link. From there, the user can login or register for a
new account. 

If the user is already logged in, then they will get a welcome screen 
giving them options to edit their profile or logout. 

## Registration

Guests may register to create a new account. User accounts are not actually
created until validated by an email sent to the user. They receive a link
containing a confirmation code. When clicked on (or pasted in manually 
after registration) the user account is then created. Once created, the user
is logged in automatically. 

Note that accounts created in this way automatically receive a role named 
“login-register”. 

### Forgot password

If you want to support a “forgot password” feature, install the
core ProcessForgotPassword module. Once installed, this module will provide
the behavior by way of that module. 

## After login

When a user is logged in, they get a welcome message with links to edit
their profile or logout. Chances are, you’ll want some other behavior. 
Here’s how you might implement that: 
~~~~~
<?php
if($user->isLoggedin() && !$input->get('profile') && !$input->get('logout')) {
  // you take over and render your own output or redirect elsewhere
  $session->redirect('/some/other/page/'); 
} else {
  // let the LoginRegister module have control
  echo $modules->get('LoginRegister')->execute(); 
}
~~~~~


### Logout link

The URL to logout is simply the URL of the page you are using this module 
on, plus a “logout” GET variable, i.e. 
~~~~~
<a href='/path/to/page/?logout=1'>Log out</a>
~~~~~

### Profile link

The URL to the profile editor is simply the URL of the page you are 
using this module on, plus a “profile” GET variable, i.e. 
~~~~~
<a href='/path/to/page/?profile=1'>Edit profile</a>
~~~~~

## Customization

This module provides a good starting point, but it's posssible you may want
to customize and add to it specific to your needs. 

### Fields

Choose the fields you want to use for both registration and profile in the 
module configuration. 

### Styles (CSS)

This module comes with only basic styles to make things workable. You should
feel free to custom style the forms and elements as you see fit. 

In the output, this module automatically adds some stylesheet links. If you
want to prevent it from doing so, see the `renderStyles` setting. 

### Scripts (JS)

In the output, this module automatically adds some script tags. If you
want to prevent it from doing so, see the `renderScripts` setting. 

This module uses some jQuery. If jQuery is not present, and `renderScripts`
is not disabled, it automatically adds a script reference to ProcessWire’s 
jQueryCore module.

### Settings

Most settings for this module are configured from the module settings 
screen (Admin > Modules > Site > LoginRegister). There are a couple of
additional settings that can be specified from the API, like this: 
~~~~~
<?php
$loginRegister = $modules->get('LoginRegister');
$loginRegister->set('renderStyles', false); 
echo $loginRegister->execute();
~~~~~
The above disables rendering of stylesheets with the output. Here is a 
list of available settings:

- `renderStyles` (boolean): Automatically render links to stylesheets used
   by the forms? Default is true.
   
- `renderScripts` (boolean): Automatically render links to scripts used
   by the forms? Default is true. 
   
All other settings can be specified from the module configuration screen.    
   
## Hooks   

Almost every aspect of this module is hookable, enabling you to modify 
or completely replace it as needed. For even more control, you might 
consider extending this module’s class, or editing it directly. For a list
of hooks available, open the LoginRegister.module file and note all 
methods listed in the phpdoc block comment at the top. These are all 
directly hookable. 

One thing that may not be clear from looking at the LoginRegister.module
file is that you might also find it useful to hook into specific Inputfield
types to add classes or make other adjustments. In this example below, we
add a hook to Inputfield objects before they are rendered, to add Uikit 3
specific classes:

~~~~~
$wire->addHookBefore('Inputfield::render', function($event) {
  $inputfield = $event->object;
  
  if($inputfield instanceof InputfieldTextarea) {
    // textarea input
    $inputfield->addClass('uk-textarea'); 
    
  } else if($inputfield instanceof InputfieldText) {
    // includes most single-line text types 
    $inputfield->addClass('uk-input');
    
  } else if($inputfield instanceof InputfieldSubmit) {
    // submit button
    $inputfield->addClass('uk-button uk-button-primary');
  }
}); 

// render module output
echo $modules->get('LoginRegister')->execute(); 
~~~~~

## License 

This module uses the same license as ProcessWire 3.x, which is the
Mozilla Public License version 2.0 (MPL 2.0). 

--------

Copyright 2017 by Ryan Cramer
