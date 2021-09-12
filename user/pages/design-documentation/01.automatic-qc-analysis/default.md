---
title: 'Automatic QC Analysis'
recaptchacontact:
    enabled: false
---

## Summary
This is the design doc for a new module called automatic QC analysis. (A better name will perhaps come to mind after working on the design a bit. Contenders: Aqua which stands for Automatic Quality Analysis) What is Automatic QC Analysis? Automatic QC Analysis takes batch QC data and determines if the batch passed for each Analyte, each Compound Class, and for the whole Method/Batch itself. Westgard Rules will be used in determining whether a batch fails or not.
The first step in allowing this analysis is data identification and data collection. Identification comes in the form of defining the Materials used for each batch, also known as Lots. Lots have a Lot Type, a name, a description, and a state. The state can be 'testing', 'active', or 'inactive'. If the Lot is in testing state, then any batch with that Lot will be defining the Mean and Standard Deviation that will be used for analyzing data.

### Automatic QC Analysis Components
The following components are necessary for automatically analyzing QC
* Lots and Lot Types
	* Each Lot is assigned to a method or methods.
	* Each Lot is defined as in the 'testing' state, 'active' state, or 'inactive' state.  

* QC Batches  
  * A batch is associated with a single method, qc data for that method, and test result data for that method
  * Since a batch has a method, and that method has Lots, the Lots in that method at the time of the Batch Assignment will automatically be inserted into the batch_lots table  
  	
* Rules  
	* Westgard rules and user defined rules  
  
* Analysis Groups  
	* An analysis group is a group of batches that the system analyzes using predefined Rules.
	
* Interactive Plots
	* Plots derived from creating different 'analysis groups' - these plots should use the data that exists and the user should be able to manipulate the data so that the plot changes accordingly.

### Implementing Aqua Components
#### Lots and Lot Types
These are implemented by creating and modifying them in the LIS through the already existing User Interface.
The first step for the user is to create a Lot Type. Trying to configure a Lot without configuring a Lot Type first will produce the following error message:  
![Configuring Lots without Lot Types](https://docs.radiumlis.com/images/aqua/ConfiguringLots-NoLotTypes.PNG)  

So create a Lot Type. The form looks like this:  
![Configuring Lot Types](https://docs.radiumlis.com/images/aqua/ConfiguringLotTypes.PNG)  

Now that we have a Lot Type, Create a Lot:  
![Configuring a Lot](https://docs.radiumlis.com/images/aqua/ConfiguringLots-NoMethods.PNG)  
! Note: This form needs to be updated when Methods are added to the form. Ideally, the user would assign the methods for the Lot when creating the Lot. There also needs to be a checkbox for "Testing" if that is part of the Lot configuration.

#### QC Batches
QC Batches are uploaded to the LIS via the folder-drop method, and when the data is extracted there will be a new page called "Batch Assigment" where Lots for the Method of the batch are automatically assigned to that batch.  
![Acquisition data for testing a lot](https://docs.radiumlis.com/user/pages/images/Table1-AcquisitionDataForTestingLot.png)

#### Rules
Radium will come preconfigured with some rule templates, shown below.  
![Predefined Rules](https://docs.radiumlis.com/user/pages/images/aqua/Table3-PredefinedRules.png)  


#### Analysis Groups
In order for automatic analysis to work with these rules, groups will need to be selected. There are many ways to go about selecting groups, but it makes sense to select groups that are in the same batch first, and then move backward in time to select others in that group. For example, if you are testing a sample in the first actual batch of a Lot (after all test batches), you cannot run any rules that require a set of 3 or more if there are only 2 points of data in that batch. 

For this to work algorithmically, the data is already grouped into batches, so we would use those batches for analysis. Here is some  pseudocode to accomplish this. The end of the output should be a matrix of failures.

The matrix might look like this:  
![Example of Failure Matrix for One Sample](https://docs.radiumlis.com/user/pages/images/aqua/SampleFailureMatrixExample.png)  

	#### Here is the code to get the final outcome from this matrix:  
	## Assume we have a samples object containing concentrations for each sample.
    ### Test Rules for counts that match the number of samples we have (Assume 12 is max number of samples per rule group)
    startPoint = 1
    endPoint = 12
	for i in range(startPoint, endPoint)
        if i > len(samples):  ## This is saying if our index i is greater than the number of samples in this batch, go to the next batch.
            break
        else:
            ## Do all rules for this index
            failed = performRuleChecks(i, batch.samples) ## This would perform all rule checks for this index for the samples
            if(failed){
                sample.failed = 1 ## Now we will know if each sample failed our rules. If we want to know which rules they failed, we can write that somewhere.
            }

	sub performRuleChecks {
		X = shift
   		samples = shift
   
   		## Return value is outcome
   		outcome = zeros(X) 
        
		## Say this index X is 4.
        ## Perform all Rules of 4 (R_4s, 4_1s, 4x)
        ## 
        
   		for rule in rules(X):
			## For example - the first rule R_4s: Reject when one in group exceeds 2s and another exceeds -2s
            outcomes(i) = performRule(X, rule, samples)
            
        return outcomes
	}
    
    sub performRule {
    	X = shift
        rule = shift
        samples = shift
        
        ## Return value is fail. Fail = 0 or 1. 0 is good.
        fail = 0
        
        ## Our example is R_4s, so perform this rule
        if(rule =~ /R_Xs/){    
        	## Reject when one in group exceeds 2s and another exceeds -2s
            exceedPos = 0
            exceedNeg = 0
            for i in range(X):
            	sample = samples(i)
                if(sample.normalized > 2){
                	exceedPos++;
                }elsif(sample.normalized < 2){
                	exceedNeg++;
                }
            
            if (exceedPos >= 1 && exceedNeg >=1){
            	fail = 1
            }
        }
        
        return fail
    }

This is just an example of the code for these rules.