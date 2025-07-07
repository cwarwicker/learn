CURRENTLY NOT WORKING

# Moodle SSO with SAML

## Setting up an example IDP

This will be using SimpleSAMLPHP as the idp. In reality this could be one of many different systems, like Shibboleth, Entra/Azure AD, Okta, Active Directory, etc...

1. Install SimpleSAMLPHP container (See: https://hub.docker.com/r/kenchan0130/simplesamlphp/) and load the test authsources so you have a few test users | Assumption: http://localhost:8081
2. You should now be able to go to Authentication -> Test Configured Auth Sources -> Example-UserPass and login with either of those 2 test users from authsources.php, e.g. `user`/`password` and see the user's attributes.
3. Using that docker image with the correct env variables, you should already have https://moodle.localhost setup as a SP on the IDP, which you can see by logging in with `admin`/`secret` and going to the Federation tab.


## Configuring with Moodle

1. Install the auth_saml2 plugin
2. Go to the plugin config settings
3. Paste the IDP metadata into the relevant setting. You get that from: "SAML 2.0 IdP Metadata" on the Federation tab in SimpleSAML. E.g. http://localhost:8081/simplesaml/saml2/idp/metadata.php
   You can either paste it or put a link to the URL.
4. Enable various debugging/logging settings in saml2 in case there's any issues
5. Leave the NameID policy as "urn:oasis:names:tc:SAML:2.0:nameid-format:transient" which I believe is the best one for SSO.
6. I chose to regenerate the certificates, don't know if it's needed or not (Also you have to turn debugging off to do so or it won't work)
7. Based on the test users we setup in SimpleSAMLPHP, set the `Mapping IdP` to `emailaddress` and map to Moodle email address.
8. Set `Auto create users` to enable, so it creates the Moodle user when we login
9. Setup the data mapping for the other fields, like name
10. Then go to Plugins -> Authentication -> SAML2 - Test settings
11. At this point it doesn;t work because of some configuraiton issues with my docker setup which I need to resolve then get back to this



## Appendix
**authsources.php**
```php
<?php
// These attributes mimic those of Azure AD.
$test_user_base = array(
    'http://schemas.microsoft.com/identity/claims/tenantid' => 'ab4f07dc-b661-48a3-a173-d0103d6981b2',
    'http://schemas.microsoft.com/identity/claims/objectidentifier' => '',
    'http://schemas.microsoft.com/identity/claims/displayname' => '',
    'http://schemas.microsoft.com/ws/2008/06/identity/claims/groups' => array(),
    'http://schemas.microsoft.com/identity/claims/identityprovider' => 'https://sts.windows.net/da2a1472-abd3-47c9-95a4-4a0068312122/',
    'http://schemas.microsoft.com/claims/authnmethodsreferences' => array('http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/password', 'http://schemas.microsoft.com/claims/multipleauthn'),
    'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress' => '',
    'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname' => '',
    'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname' => '',
    'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' => ''
);

$config = array(
    'admin' => array(
        'core:AdminPassword',
    ),
    'example-userpass' => array(
        'exampleauth:UserPass',
        'user1:password' => array_merge($test_user_base, array(
            'http://schemas.microsoft.com/identity/claims/objectidentifier' => 'f2d75402-e1ae-40fe-8cc9-98ca1ab9cd5e',
            'http://schemas.microsoft.com/identity/claims/displayname' => 'User1 Taro',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress' => 'user1@example.com',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname' => 'Taro',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname' => 'User1',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' => 'user1@example.com'
        )),
        'user2:password' => array_merge($test_user_base, array(
            'http://schemas.microsoft.com/identity/claims/objectidentifier' => 'f2a94916-2fcb-4b68-9eb1-5436309006a3',
            'http://schemas.microsoft.com/identity/claims/displayname' => 'User2 Taro',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress' => 'user2@example.com',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname' => 'Taro',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname' => 'User2',
            'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name' => 'user2@example.com'
        )),
    ),
);

```
