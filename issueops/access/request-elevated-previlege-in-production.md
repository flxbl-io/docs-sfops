# Request elevated previlege in production

<figure><img src="../../.gitbook/assets/image (17).png" alt=""><figcaption></figcaption></figure>

This issue and its associated configuration allows you to provide different access levels for developers who need temporary elevated privileges in a production  org. This feature is particularly useful in scenarios where developers require limited-time access to perform specific tasks or troubleshoot issues directly in the production environment.

### Why Use Elevated Access Levels?

1. **Controlled Access**: The elevation system allows you to grant developers specific elevated privileges for a defined duration. This ensures that developers have the necessary access to perform their tasks without permanently granting them excessive permissions.
2. **Flexibility**: You can define multiple access levels, each with its own set of assigned profiles, permission sets, permission set licenses, and duration. This flexibility enables you to tailor the access based on the specific requirements of different developer roles or tasks.
3. **Time-bound Access**: By specifying a duration for each access level, you can ensure that the elevated privileges are automatically revoked after a certain period. This helps maintain the principle of least privilege and reduces the risk of prolonged unnecessary access.
4. **Streamlined Process**: With the elevation configuration file, you can predefine the access levels and their associated permissions. This streamlines the process of granting elevated access to developers, reducing the need for manual intervention and ensuring consistency across different elevations.
5. **Auditability**: The elevation system provides a clear record of who was granted elevated access, what permissions were assigned, and for how long. This auditability is crucial for compliance and security purposes, allowing you to track and review the elevated access granted to developers.

### YAML Configuration Structure

The YAML configuration file for developer elevation has the following structure:

```yaml
devconfig:
  accessLevels:
    <access-level-name>:
      profile: <profile-name>
      permissionSets:
        - <permission-set-name>
        - <permission-set-name>
      permissionSetLicenses:
        - <permission-set-license-name>
      permissionSetGroups:
        - <permission-set-group-name>  
      duration: <duration-in-hours>
```

* `accessLevels`: This section defines the different access levels available for developers.
  * `<access-level-name>`: Replace this with a meaningful name for the access level (e.g., "developer", "support").
    * `profile`: Specify the profile to be assigned to the developer for this access level.
    * `permissionSets` (optional): List the permission sets to be assigned to the developer for this access level.
    * `permissionSetLicenses` (optional): List the permission set licenses to be assigned to the developer for this access level.
    * `permissionSetGroups` (optional): List the permission set groups to be assigned to the developer for this access level.
    * `duration`: Specify the duration (in hours) for which the elevated access will be granted.

### Example Configuration

Here's an example configuration that demonstrates different access levels:

```yaml
devconfig:
  accessLevels:
    developer:
      profile: Developer Limited Access
      permissionSets:
        - DX_Limited_Access
        - Prod_Access_48hrs
      permissionSetLicenses:  
        - Salesforce DX
      duration: 48
    support:
      profile: Support Limited Access
      permissionSets:
        - Support_Access
        - Prod_Access_8hrs 
      duration: 8
    ci-cd:
      profile: CI/CD Limited Access
      permissionSetGroups:
        - DevOps_Tools_Access
        - Prod_Deploy_Access
      duration: 4
```

In this example:

* The `developer` access level grants the "Developer Limited Access" profile, assigns the "DX\_Limited\_Access" and "Prod\_Access\_48hrs" permission sets, assigns the "Salesforce DX" permission set license, and sets the duration to 48 hours.
* The `support` access level grants the "Support Limited Access" profile, assigns the "Support\_Access" and "Prod\_Access\_8hrs" permission sets, and sets the duration to 8 hours.
* The `ci-cd` access level grants the "CI/CD Limited Access" profile, assigns the "DevOps\_Tools\_Access" and "Prod\_Deploy\_Access" permission set groups, and sets the duration to 4 hours.

You will need to create an equivalent `devconfig.yaml` file and place it in the config folder in your project repository.
