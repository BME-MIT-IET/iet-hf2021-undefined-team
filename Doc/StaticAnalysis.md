# Static Analysis by SonarQube

*For Static analysis , I choose SonarQube , even it doesn't support for C/C++ officially ,But there are cppcheck on github which is open sourced.*

## Installation

#### 1.Download Sonarqube:

![1](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/1.PNG)

Unzip it.

![2](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/2.PNG)

Add the bin to the system path.

![3](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/3.PNG)

Choose the specific system .

#### 2.Run the Sonarqube server:

![4](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/4.PNG)

Run the StartNTService

![5](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/5.PNG)

Change the properties in conf folder, then StartSonar (use the Admin).

![6](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/6.PNG)

If the SonarQube is up , then your setting correctly.

![7](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/7.PNG)

Open localhost:9000 (Defalut port),then you can see the UI of the sonarqube.


#### 3.Add the sonarscanner and cppchecker

Download the sonarscanner and add the bin into system path as last step.
Download the cxx-plugin to the sonarqube

![8](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/8.PNG)
xx\sonarqube-8.9.0.43852\sonarqube-8.9.0.43852\extensions\plugins

After that , restart sonarqube.

If the setting is correct , you can see the rules of the cppcheck in **Rules**

***

Then we need to set a new **Quality Profile**:

Create ---> Name,Language:cxx,parent:none
And add the rules of the cppchecker.

After succesfull setting , we can use the sonarscanner with open source cppchecker

![9](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/9.PNG)


## Run the test 


Run the scanner under the project you want to check , after success execution , go back to server , you can see the result of the scan.

![10](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/10.PNG)


***

## Get the result
We cam see that there are no bugs and vulnerabilities . but still , it didn't pass the **Quality Gates** ,  the main reason is the maintainability is less than A , also the Duplicates lines are too much , also in the report , we can see many **Code Smells** 


![11](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/11.PNG)

![12](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/12.PNG)

![13](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/13.PNG)



# Manual code review 

During the review of the previous commits , I found they were working on the code completion ,creating the classes and move the parameters/functions to another header/source file to make the code clear and easy to understand .

![14](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/14.PNG)


![15](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/15.PNG)

Later they have some fix with warnings 


![16](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/16.PNG)

![17](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/17.PNG)

Also they made some code cleaning improvement .

![18](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/18.PNG)


Fix codes with bugs 


![19](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/19.PNG)


![19](https://github.com/BME-MIT-IET/iet-hf2021-undefined-team/blob/main/IMG/20.PNG)