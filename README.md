# PolCloud
Run PolNet in the cloud! 

## Set up tasks

### Azure terminology
* In the portal, **blades** can be selected from the list on the left
  (many appear under "More services" at the bottom of the list - this
  is searchable)
  
* A **service principle** is a user-like account tied to an
  application on Azure. See
  https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-application-objects
  for more details.

* A **tenant** 


### Allow Batch to access your resources
Note that you need to have owner level priviledges for your
subscription to do this.

* Register the Microsoft.Batch resource provider for your subscription
  in the portal. Go to subscriptions blade, choose your subscription,
  then resource providers. Search for "Microsoft.Batch" and ensure it
  is registered.
  
* Add the Microsoft Azure Batch service principal as a collaborator to
  your subscription in the portal. In your subscription, choose Access
  control (IAM), then add. For the role, pick contributor then search
  for "Microsoft Azure Batch" or "MicrosoftAzureBatch" (case and space
  matters and it appears random whether spaces are necessary or
  not...).
  

### AzHelp.Auth
* Create an App user for your batch use. In the portal go to "App
  registrations" then "new". Give it a name, the type is native and
  sign on URI can be http://AnythingYouLikeThatIsAValidUri. Give it
  access to Batch, by going to required permissions, add, 1) select
  API "Microsoft Azure Batch", 2) select permissions "Access Batch"
  requires admin = no. Generate a key with any name and duration you
  like. COPY THE SECRET! You need it below.
  

* Create a configuration file for AzHelp.Auth in `~/.azure/polnet.json`
```
{
   "credentials" : [
	{
	    "name": "default",
	    "subscription_id": "the hex string identifying your azure subscription",
	    "tenant_id": "hex string identifying your AD tenant",
	    "client_id": "i just use the az cli client",
	    "secret": "corresponding secret key"
	},
	{
	    "name": "whatever - maybe 'batch'",
	    "subscription_id": "as above",
	    "tenant_id": "as above",
	    "client_id": "application id from above",
	    "secret": "secret key generated above"		
	}
   ]
}
```

## Workflow

1. Setup tasks - cost tiny amounts and must be done with credentials
   that can create resource groups.
    1. Create the batch account and related infrastructure with
	`polcloud.DeployBatch`
   
    2. Authorise your app to access the resource group you created as
	a contributor (manual task in the portal at the moment - go to the
	resource group, Access control, )
   
    3. Create an image with `polcloud.ImageCreator`
    
    4. (Optionally) test that it works as intended with
    `polcloud.ImageTester`
    
2. Run tasks - note that this costs money as long as the pool is
   running! These can be done with the credentials

    5. Create a pool with `polcloud.CreatePool`
	
	6. Add one or more jobs with `polcloud.CreateJob`
	
	7. Delete the pool with `polcloud.DeletePool`




