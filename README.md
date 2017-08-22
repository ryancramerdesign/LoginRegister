# Login and Registration

A module for ProcessWire 3.x that provides a self contained process that
provides a login form, user registration, and profile editor. 

## Installation

Install the module as with any other module. After installation, either
create a new template or use an existing template to use this module with.
This module will provide the “content” or “body copy” area of your page,
and that’s where you should use its output. Edit your template file and 
place the following in it:

**Direct output:**
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

This module provides a good starting point, but chances are you’ll want to
customize and add to it specific to your needs. 

### Fields

The fields collected for registration and profile are consistent with the
defaults available in ProcessWire. If you want to add support for more 
fields that you might have added to your user template, you can do so with 
hooks that this module provides. 

As an example, lets say that we've added a “title” field to our user template
that contains the user’s full name, and we want this to be present on the
profile editor. We would add the following hook in your template file:
~~~~~
<?php
$loginRegister = $modules->get('LoginRegister');

$loginRegister->addHookAfter('buildProfileForm', function($event) {
  $form = $event->return;
 
  // create our new Inputfield
  $title = $event->wire('modules')->get('InputfieldText');
  $title->attr('name', 'profile_title'); 
  $title->label = __('Full name'); 
  
  // insert our new field after the email field
  $email = $form->getChildByName('profile_email'); 
  $form->insertAfter($title, $email);  
}); 

echo $loginRegister->execute();
~~~~~
Note that any fields you add to the profile form must have names prefixed 
with “profile_”. The second part of the field name must be the name of the
field in ProcessWire. So if we are creating an Inputfield to manipulate the
“title” field, the field name would be “profile_title”. 

The registration form works the same way, except that the field names are 
prefixed with “register_” rather than “profile_”. 

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

You may adjust various settings of this module before calling the 
`execute()` method, like this:
~~~~~
<?php
$loginRegister = $modules->get('LoginRegister');
$loginRegister->set('allowRegister', false); 
echo $loginRegister->execute();
~~~~~
The above disables new user registration and makes the module only provide
login functionality. Here is a list of available settings:

- `allowForgot` (boolean): Allow use of forgot password function? The default
   value is true if the ProcessForgotPassword module is installed, or false
   if it isn't. 
   
- `allowRegister` (boolean): Allow new user registration? Default is true.

- `renderStyles` (boolean): Automatically render links to stylesheets used
   by the forms? Default is true.
   
- `renderScripts` (boolean): Automatically render links to scripts used
   by the forms? Default is true. 
   
- `roleName` (string): Name of user role to automatically add to new users
   that have their account created by registration through this module. 
   The default value is “login-register”, which is the name of a role 
   added by this module during installation. 

## Hooks   

Almost every aspect of this module is hookable, enabling you to modify 
or completely replace it as needed. For even more control, you might 
consider extending this module’s class, or editing it directly. For a list
of hooks available, open the LoginRegister.module file and note all 
methods listed in the phpdoc block comment at the top. These are all 
directly hookable. 

--------
Copyright 2017 by Ryan Cramer
