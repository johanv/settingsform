# cards.iwwa.settingsform

Actually I 'stole' all this from
https://github.com/eileenmcnaughton/nz.co.fuzion.civixero/blob/master/CRM/Civixero/Form/XeroSettings.php
so credits to
[Eileen](https://github.com/eileenmcnaughton).

## How does it work?

Install and enable this extension.

Create a file in your extension `settings/MyExtension.setting.php`,
containing something like this:

```
<?php

/*
 * Settings metadata file
 */
return array(
  'myextension_some_setting' => array(
    'group_name' => 'MyExtension Preferences',
    'group' => 'myextension',
    'name' => 'myextension_some_setting',
    'type' => 'String',
    'default' => '',
    'add' => '4.7',
    'is_domain' => 0,
    'is_contact' => 0,
    'description' => 'Description of this setting',
    'title' => 'Title of this setting',
    'help_text' => 'Help text of this setting.',
    'html_type' => 'Text',
    'quick_form_type' => 'Element',
  ),
  // Add more settings here.
);
```

The configuration of the setting using a key-value-array is used at
more places in CiviCRM. I'm not sure about the fields and the possible
values. Maybe there is documentation somewhere.

The important thing is that all your settings have the same value for
`group`.

You also need to create a form class 
`CRM_MyExtension_Form_MyExtensionSettings` in your extension, like this:

```
<?php

class CRM_MyExtension_Form_MyExtensionSettings extends CRM_Settingsform_Form_Settings
{
  function buildQuickForm()
  {
    $this->setSettingFilter(['group' => 'myextension']);
    parent::buildQuickForm();
  }
}
```

(Note that the same group name is used in `setSettingFilter`.)

At last, put the following in
`xml/Menu/myextension.xml`:

```
<?xml version="1.0"?>
<menu>
    <item>
        <path>civicrm/myextension/settings</path>
        <page_callback>CRM_MyExtension_Form_MyExtensionSettings</page_callback>
        <title>My Extension Settings</title>
        <access_arguments>access CiviCRM</access_arguments>
    </item>
</menu>
```

Now you can access your settings form by surfing to
civicrm/myextension/settings on your CiviCRM instance. You can access
the settings e.g. using the API:

```
    $settingValue = '';

    $apiResult = civicrm_api3(
      'Setting',
      'get',
      ['return' => 'myextension_some_setting']
    );

    if ($apiResult['count']) {
      $settingValue = CRM_Utils_Array::first($primaryRelationshipResult['values']);
    }
```

Default valus for your settings can be stored in `civicrm.settings.php`
like this:

```
$civicrm_setting['myextension']['myextension_some_setting'] = 'the default setting';
```
