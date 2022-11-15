
  
  

# Developer-Operations

  

  

**A globally scoped custom ServiceNow application that allows you to execute custom background scripts directly from forms and lists with additional inputs and outputs.**

  

  

I built this little tool as an easier way to execute background scripts without having to go digging around in my scripts folder and then pasting it into a code editor. Modifying the inputs and finally running it in a background script.

  

  

With this you can do the following:

  

- Execute operations on one or many records.

- Execute operations in a background processor.

- Create an operation of your own.

- Copy the script directly from form.

- Generate and return a CVS file from your operation.

  

  

## How to get

  

You can either fork this branch to import it to your instance or just download the repository from and import the DeveloperOperationsUpdateSet file directily in your instance. 

  

## Execute an operation

Executing an operation is extremely simple. As long as you are in the base platform UI (either a form or a list view) you can access the developer operations through the UI action.

  
  

![](Docs/RelatedLink.png)

![](Docs/RelatedLink-List.png)

  
  

This opens up the Developer Operations modal which embeds a ServicePortal widget.

![](Docs/OperationsInput.png)

  

Input Form Elements

  

1. A drop down with all available operations that can be executed on the selected record(s).

2. A copy button which will dynamically generate a script into your clipboard if you want to execute this logic outside of your current ServiceNow instance.

3. Variable inputs based on which operation you have selected.

4. A toggle to optionally execute the operation in a background processor (mostly useful for longer running operations).

5. And obviously, the execute button.

  

The operations that you will see are are similar to business rules in the sense that they will only be available to execute for operations that have been configured for the specific tables (with filtering) that you currently are on.

  

Once you execute the operation, the background script will run and will display whatever output the operation script returned.

![](Docs/OperationsOutput-markup.png)

Output Form Elements

  

1. The display value of the record that the execution was executed on.

2. The status in the form of a check mark or an asterisk to indicate if the script executed successfully or failed.

3. The returned output of the operation.

4. A selector to flip through each of the operation executions.

- Note: This only applies if the operation was executed from a list view on multiple rows.

5. A link to the system logs if you want to view all of the logs that took place between the start and end time of the operation execution.

6. A copy output to clipboard button.

  

## Create an operation

Although the developer operations tool does come with some out of of the box scripts that I personally created for myself just to use in my own day to day, the real advantage to this tool is that its extremely simple to take the existing scripts that you have for the ServiceNow platform and create operations with them.

  

The list of operations is available in the Dev Tool Operations [u_dev_tool_operation] table. In the filter navigator you can get to it under **Developer Operations > Operations**. This table extends Application Files so that different suites of operations can be packaged up in update sets according to their use cases.

  

Go to the Dev Tool Operations table list view and click **New** to get started.

![](Docs/Operations%20Form%20%28New%29.png)

- Dev Tool Operations form Input:
	- Name (String)
	- Table (Table Name): The table that this operation applies to. NOTE: if set to the "global" table, this operation will appear on everything.
	- Active (Boolean)
	- Conditional (Boolean): Allows additional filtering on the operation. This will restrict the operation so that it will only execute under specific conditions.
	- Condition (Conditions)
	- Description (String)
	- Type (Choice)
		- Basic: Operation script accepts one GlideRecord at a time. If multiple rows are selected at once, one operation instance will be executed per row.
		- List: Operation script accepts a list of GlideRecords based on the list views current active filter. Operation can then execute logic on multiple rows at the same time.

	- Script (Script): Depending on the operation type, the script will automatically have the current GlideRecord (Basic) or the list of GlideRecords (List) into the script along with any additional user input configured by the user.

1. Set the name and description of your operation and select which table and type you want to run the operation.

2. If the script you have is targeted towards a bulk list of records that cant be considered children under a specific parent record, the List operation type is ideal, otherwise the Basic type should be fine in most cases.
3. After a table has been selected you can configure additional filtering if necessary but otherwise you can save the form.
	![](Docs/Operations%20Form%20%28Saved%29.png)
4. Configure the script. All necessarily GlideRecord and input params are passed though automatically to be used withing the script. 
	- Return Types:
		- String: will appear in a pre tag on the form. If an object is returned the output will stringify the object prior to displaying it.
		- GlideRecord: will appear as a link allowing users to go directly to whatever record that could have resulted form executing the operation.

### Configure additional Operation inputs
Aside from the default inputs of an Operation, Operations can also be configured with additional inputs that the users will fill out on the modal form prior to execution. This allows for the operation script to accept additional inputs that could be useful during execution.

All operations inputs are in the Dev Tool Operations Inputs [u_dev_tool_operation_input] table and can be created/edited from its parent Operation related lists.
![](Docs/Operations%20Input%20%28List%29.png)
You can either imediatly begin adding inputs directly from the related list or you can click **New**.
![](Docs/Operations%20Input%20%28New%29.png)
 - Dev Tool Operation Input form inputs
	 - Dev tool operation (Reference): The parent Operation that this input will appear under.
	 
	 - Input label (String)
	 
	 - Input name (String)
	 
	 - Max length (Number)
	 
	 - Active (Boolean)
	 
	 - Mandatory (Boolean)
	 
	 - Type (Choice): Different input types are supported allowing you to get more specific with the intentions of the input instead of just assuming users are going to enter the correct input though a string.
	 
		 - Text: Default input type. A plain text field allowing the user to input whatever they want.
		 
		 - Number: Instead of displaying a text field on the form, a number input type will appear instead.
		 
		 - Reference: Input will be replaced with a sn-record-picker searchable choice list. When the input is passed through to the Operations script, the selected record will be accessible as a GlideRecord.
		 
			 - Reference (Table name): The table that the sn-record-picker searches for the reference input type.
			 
			 - Display Field (Field name): The display column that the user searches when using the sn-record-picker.
			 
			 - Qualifier (Condition): Similar to a reference qualifier on columns, add additional filtering to the search to help limit the results returned to the user.
			 
		 - True/False: A simple boolean select input in the case that a simple setting can be toggled on or off during the script execution.
		 
		 - Choice: Allows you to create a list of choices for this input that will then appear to the user as a select input.
		 
			 - Choice list (Simple name values): A simple name values list builder allowing you to define the Name and Value for each choice.
		
Here is an example of an operation where the input requires a user to define a target application scope as long as the target application has an active status of true.
![](Docs/Operations%20Input%20%28Saved%29.png)

### Use Inputs within the Operation script
When an operation has child input records, these inputs are automatically injected into the Operation script during execution and can be accessed by simply using the variable name that matches with the name value of the input.

Below, is an example using the same input from the Configure additional Operation inputs section above. We simply can get the **target_application** at the start of the script. Because input type is a reference, target_application is a GlideRecord.
![](Docs/Operations%20Form%20%28Input-Script%29.png)

**Note**: Just like a UI action, **current** is automatically passed through as well and is the GlideRecord of the current record that this operation is being executed on. It is important to not name any of the inputs with the name "current" as this can create conflicts on the variable.




