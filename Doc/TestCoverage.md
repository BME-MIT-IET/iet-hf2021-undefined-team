# Test Coverage

The purpose of doing test covearage for the given Library, Recommender, project was to make sure which lines and loops were participating during the execution of the program. With the help of OpenCppCoverage software we were able to find test_covergae regarding all the folders which were present in the given repository.

- Hiredis
- Kfold 
- Shuffle
- Tests

## **Shuffle Folder Test Coverage**:

![image](https://user-images.githubusercontent.com/68583803/118409259-ce215d80-b689-11eb-825b-3fd7e80c059c.png)

<br />

### **Solution**:

It's also showing the warning messages along with some errors. The reason behind all these messages and errors are the usage of unsafe string function in the program such as **strcpy(), strlen() etc** . We can easily resolve these minor messages with the help of iteration of the loop or some other easy built-in functions.

<br />
&nbsp;


## **Kfold Test Coverage**:

While working with the kfold test coverage the test failed just because of pointer-type issue. The issue has been covered underneath:

![image](https://user-images.githubusercontent.com/68583803/118409793-7afcda00-b68c-11eb-9d97-be9010fe6c35.png)

<br />

 ### **Solution:**

In order to get the test coverage tool to work properly, we work with the code in k_fold_parameters.c file and resolved all the issues regarding this coverage blocker and got our results which can be found in the attachment below:

![image](https://user-images.githubusercontent.com/68583803/118409884-e5157f00-b68c-11eb-92ea-6d5bb316eb1c.png)


<br />
&nbsp;

 ## **Tests Test Coverage**:
This is already testing done by the library program. With the help of OpenCPPCoverage, we were able to determine which files contain the maximum number of lines execution while program compiling. Information related to all these things can find below:
Tests Folder Test Coverage:

![image](https://user-images.githubusercontent.com/68583803/118409365-5f90cf80-b68a-11eb-8d47-8758cf10d000.png)

<br /> 

 ### **Solution:**
 
 ALL THE HEADER AND CPP FILES IN this FOLDER TOOK PART WHILE TESTING THE INPUTS WHICH CAN give US THE IDEA OF HOW MUCH THIS LIBRARY PROGRAM IS RELIABLE AND ERROR-FREE.

<br />
&nbsp;


## **Hiredis  Test Coverage**:

Finally, we were able to finish the test coverage of the last folder of the given library which you can find below:

![image](https://user-images.githubusercontent.com/68583803/118410212-9d8ff280-b68e-11eb-9441-11148ded45a3.png)

<br />

### **Solution:**

 There were some warnings while covering the test coverage of this folder because of using unsafe string builder functions which we can resolve by using loops and conditional statements etc.