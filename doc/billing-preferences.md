# Billing preferences client

## General information

This API will allow you to access and edit your preferences and retrieve your custom billing statements.

## Entities

### Preference
The ```Preference``` entity allows to manage the company's main contact:

| Field          | Type         | Example            | Description                                            |
|----------------|--------------|--------------------|--------------------------------------------------------|
| identifier     | ```string``` | GroupBy            | Preference type (see below for the full list of types) |
| parameters     | ```array```  | [ columns => ... ] | Associative list of parameters (depends on the type)   |

## Preference Types
The ```identifier``` field can be one of thoses preference types:

### GroupBy
For the GroupBy type, you must set a list of columns in the ```parameters``` field.

| Parameter | Type        | Example         | Description               |
|-----------|-------------|-----------------|---------------------------|
| columns   | ```array``` | [ BillingType ] | List of groupable columns |

Available columns:

 - CreatedBy
 - ResourceGroup
 - Name
 - CostCenter
 - Project
 - Application
 - Environment
 - CustomTag
 - SubscriptionFriendlyName
 - ArsSubscriptionId

## Usage

### Initialization
The "billing preferences" client is simply called ```PreferencesClient```.
You can get it through the main entry point ```PublicApiClient``` and its method ```getBillingPreferencesClient()```, or instanciate it directly:
```php
<?php

use ArrowSphere\PublicApiClient\Billing\PreferencesClient;

const URL = 'https://your-url-to-arrowsphere.example.com';
const API_KEY = 'your API key in ArrowSphere';

$client = (new PreferencesClient())
    ->setUrl(URL)
    ->setApiKey(API_KEY);

```

### List all the preferences
You can list all the preferences by calling the ```getPreferences()``` method.

This method returns a ```Generator``` and yields instances of the ```Preference``` entity.

Example:
```php
<?php

$period = '2021-04';
$preferences = $client->getPreferences($period);
foreach ($preferences as $preference) {
    echo $preference->getIdentifier() . PHP_EOL;
}
```

### Create preferences
You can create a new list of preferences by calling the ```createPreferences()``` method.

Note: You can't create preferences in the past.

Example:

```php
<?php

use ArrowSphere\PublicApiClient\Billing\Entities\Preference;
use ArrowSphere\PublicApiClient\Billing\Enum\PreferenceGroupByColumnsEnum;
use ArrowSphere\PublicApiClient\Billing\Enum\PreferenceTypeEnum;

$period = '2021-04';
$preferences = [new Preference([
    Preference::KEY_IDENTIFIER => PreferenceTypeEnum::GROUP_BY,
    Preference::KEY_PARAMETERS => [
        Preference::KEY_COLUMNS => [
            PreferenceGroupByColumnsEnum::CREATED_BY
        ],
    ],
])];

$client->createPreferences($period, $preferences);
```