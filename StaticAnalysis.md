#Static Analysis by SonarQube

*For Static analysis , I choose SonarQube , even it doesn't support for C/C++ officially ,But there are cppcheck on github which is open sourced.*
##Installation
###Install SonarQube
####1.Download Sonarqube:
![Folder](IMG\1.PNG)
Unzip it.

![Folder](IMG\2.PNG)
Add the bin to the system path.

![Folder](IMG\3.PNG)
Choose the specific system .

####2.Run the Sonarqube server:
![Folder](IMG\4.PNG)
Run the StartNTService

![Folder](IMG\5.PNG)
Change the properties in conf folder, then StartSonar (use the Admin).

![Folder](IMG\6.PNG)
If the SonarQube is up , then your setting correctly.

![Folder](IMG\7.PNG)
Open localhost:9000 (Defalut port),then you can see the UI of the sonarqube.


####3.Add the sonarscanner and cppchecker
Download the sonarscanner and add the bin into system path as last step.
Download the cxx-plugin to the sonarqube
![Folder](IMG\8.PNG)
xx\sonarqube-8.9.0.43852\sonarqube-8.9.0.43852\extensions\plugins

After that , restart sonarqube.

If the setting is correct , you can see the rules of the cppcheck in **Rules**
***
Then we need to set a new **Quality Profile**:

Create ---> Name,Language:cxx,parent:none
And add the rules of the cppchecker.

After succesfull setting , we can use the sonarscanner with open source cppchecker

![Folder](IMG\9.PNG)
Run the scanner under the project you want to check , after success execution , go back to server , you can see the result of the scan.

![Folder](IMG\10.PNG)
