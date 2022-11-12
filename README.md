

# Developer-Operations

  

 **A globally scoped custom ServiceNow application Modal UI with embedded widget to execute custom background scripts with inputs and outputs.**

  

I built this little tool as an easier way to execute background scripts without having to go digging around in my scripts folder and then pasting it into a code editor. Modifying the inputs and finally running it in a background script.

  

With this you can do the following:

 - Execute operations on one or many records. 
 - Execute operations in a background processor.
 - Create an operation of your own.
- Copy the script directly from form.
- Generate and return a csv file from your operation.

  

## How to get

You can either fork this branch to import it to your instance or just download the update set here. [DeveloperOperationsUpdateSet(11-11-2022.xml)](DeveloperOperationsUpdateSet%2811-11-2022%29.xml)

## Execute an operation
Executing an operation is extremely simple. As long as you are in the base platform UI (either a form or a list view) you can access the developer operations through the UI action.


![](Docs/RelatedLink.png)  					
![](Docs/RelatedLink-List.png)


This opens up the Developer Operations modal which embededs a ServicePortal widget.
![](Docs/OperationsInput.png)

Input Form Elements

 1. A drop down with all available operations that can be executed on the selected record(s).
  
 2. A copy button which will dynamicly generate a script into your clipboard if you want to execute this logic outside of your current ServiceNow instance.
 3. Variable inputs based on which operation you have selected.
 4. A toggle to optionally execute the operation in a background processor (mostly usefull for longer running operations).
 5. And obviously, the execute button.

The operations that you will see are are similar to business rules in the sense that they will only be available to execute for operations that have been configured for the spesific tables (with filtering) that you currently are on.

Once you execute the operation, the background script will run and will display whatever output the operation script returned.
![](Docs/OperationsOutput-markup.png)
Output Form Elements

 1. The dispaly value of the record that the execution was executed on.
 2. The status in the form of a checkmark or an asterisck to indicate if the scipt executed successfuly or failed.
 3. The returned output of the operation.
 4. A selector to flip through each of the operation executions.
	 - Note: This only applies if the operation was executed from a list view on multiple rows.
 5. A link to the system logs if you want to view all of the logs that took place between the start and end time of the operation execution.
 6. A copy output to clipboard button.

## Create an operation
Although the developer orperations tool does come with some out of of the box scripts that I personally created for myself just to use in my own day to day, the real advantage to this tool is that its extremely simple to take the existing scripts that you have for the ServiceNow platform and create operations with them.


