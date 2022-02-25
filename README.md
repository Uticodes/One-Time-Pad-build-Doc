# MPP Build Process Documentation


## Things to know before making a build

We have several things to note before making a build, first thing would be reviewing the ticket carefully to know what you need to do for that build,
the most important things to note would be the Branding if any, EMV config if any, DexGuard Licence if any, SHA Hash Key if any, package name, reader type, build type, and Environment Confluence.

Some build types we have are:
1. externalDev
2. internalQA
3. secure
4. pilot (Production)
BuildTypes defines what type of builds we're making.

Some readers type (readers_supported) are: 
The readers types are:
1. InDevice
2. InDevicePin
3. Datecs
4. Dspread
Readers defines the type of readers the app supports.


## Steps to follow to make different builds

We are going to use some tickets some tickets that we've worked on already as our example tickets for this process.

When a ticket is given, e.g https://mypinpad.atlassian.net/browse/VUBSVK-33
The requirement is to:
1. Update EMV config file,
2. Hide App icon from phone menu

To achieve this, we have to go to the project on our local machine
Right now I believe you have the project setup on your PC.
On the projeect, first checkout to develop branch, git pull to get latest changes, then git checkout -b [branach-name] (in this case we use the ticket number plus description for the branch name, e.g VUBSVK-33/update_emv_config) to create a new branch for the task,

Now checking on the ticket, the title say ```New VUB Production build with correct EMV Configuration``` meaning that we are to make a Production build.
So let's navigate to where we can update our EMV config file, on the project (android-goodfellow), then open ```app```, then open ```src```, open ```client``` in here you'll see packages for different clients, for the seek of this example, we need ```circleblueintesa```, open ```circleblueintesa```, open ```indevice```, open ```vub```, inside of it we have two packages, ```dev``` and ```live```, dev is for development builds, while live is for production builds, so we'll open ```live => res => raw```, inside raw we have ```emv_config.json``` this is the file we need to update,

![image](https://user-images.githubusercontent.com/43546652/155621075-a30e9922-a3b3-46d7-9ba6-c84089462a76.png)

We can now go to the ticket and download the attached emv file, then replace with the one currently on the app.

![Screenshot 2022-02-24 at 11 57 00 PM](https://user-images.githubusercontent.com/43546652/155621687-14ee3332-dffb-4438-bd29-688f4f272ea3.png)

 Once done with updating the emv, next would be to HIDE THE APP ICON ON DEVEICE. To do that we need to go to the Manifest file for the client (Manifiest file inside circleblueintessa) and add this two line of code inside the activity
```
tools:ignore="AllowBackup,MissingApplicationIcon"
tools:node="mergeOnlyAttributes"
```

After that, next thing that should be done, would be to update build version on ```app build.gradle```, increase the patch number, example from 27 to 28.

![image](https://user-images.githubusercontent.com/43546652/155623064-589ba1f4-27a1-4b59-8216-637843c2b5c9.png)

Now we're ready to make a PR. Once PR is review/approve and Complete, go back to terminal and checkout to develop branch, pull to get update branch, checkout to qa branch, pull to update qa branch, rebase develop into qa, now we're ready to make our build tags

#Making Build tags 
First thing we use for the build tag is the build.gradle versioning e.g 1.58.28, then RELEASE-1.58.28-INDEVICEPIN-SODIUM_VUB_LIVE-PILOT, how we get this, ```1.58.28```
we know is the versioning number, ```INDEVICEPIN``` is gotten from the ticket, Reader will be spacified, ```SODIUM_VUB_LIVE``` endpoint name (sodium-live-vub), it can be gotten by navigating to where we have all the endpoints, open ```prod.yaml```, search for VUB, then ```PILOT``` is for live.

![image](https://user-images.githubusercontent.com/43546652/155624703-9d25c82d-2464-4329-afd2-6101c633ef29.png)

on the terminal type ```git tag 1.58.28``` enter, then ```git tag RELEASE-1.58.28-INDEVICEPIN-SODIUM_VUB_LIVE-PILOT``` enter, then ```git push --tags```, the ```git push``` to push all changes on qa branch.
Now head over to the browser, open ```android-ternimal-app``` repo, click on Pipelines, select Pipeline on the dialog that appears, 

![Screenshot 2022-02-25 at 12 36 18 AM](https://user-images.githubusercontent.com/43546652/155625796-ba54152f-d88f-42cf-b316-a830a3a981e6.png)


Then locate the ```Goodfellow-CI-Android-Prod```

![Screenshot 2022-02-25 at 12 38 25 AM](https://user-images.githubusercontent.com/43546652/155625858-36791769-05e9-4353-a609-4db06c98c8a8.png)

Then select ```Run pipeline```

![Screenshot 2022-02-25 at 12 42 09 AM](https://user-images.githubusercontent.com/43546652/155626004-eae82d90-ada7-4d05-bd5f-fe71e0c48194.png)

Click on develop, click on tags, enter the tag number, enter the endpoint name and the Reader type, then run the build.


See short samle clip below

https://drive.google.com/file/d/1nkLRVtg4nkULJOHL24Wt0dCpuNY8e0Bc/view?usp=sharing

Once the build is done, click to open, clip on artifact to get the build, or click on published to get the build,

![Screenshot 2022-02-25 at 1 30 30 AM](https://user-images.githubusercontent.com/43546652/155630828-fdca1a63-55f2-4b62-a79e-0bef1958a8fe.png)

Donload build with protected and mapping file
![Screenshot 2022-02-25 at 1 34 50 AM](https://user-images.githubusercontent.com/43546652/155631028-9c79939d-e76d-4105-9d22-d85ae4a31980.png)

You can then use clientside-wiki project to make a folder for your build

Sample templete
```
#externalDev , internalQA , secure , pilot
buildTypes=(“pilot”)
#InDevice , InDevicePin, Datecs , Dspread
readers=(“InDevicePin”)
envs=(“tungsten-live” “sodium-live”)
version=“1.56.13”
```

![image](https://user-images.githubusercontent.com/43546652/155631392-e06330f7-90ec-4ec6-8e5a-5f075ffd8327.png)


Then you can now upload your build to ```Release Candidate - Android``` https://licentiagroup.sharepoint.com/sites/GoodfellowOpenMPOS/Shared%20Documents/Forms/AllItems.aspx?csf=1&web=1&e=yXP5An&cid=f9db9a1f%2D4022%2D45f9%2D8a5e%2Dcc2edbfdffb5&FolderCTID=0x012000CF477C05AEE46B4897359A94FBA55777&OR=Teams%2DHL&CT=1645618624771&sourceId=&params=%7B%22AppName%22%3A%22Teams%2DDesktop%22%2C%22AppVersion%22%3A%221415%2F22010300411%22%7D&id=%2Fsites%2FGoodfellowOpenMPOS%2FShared%20Documents%2FRelease%20Candidates%20%2D%20Android%2FApks%2FRelease%20Candidates&viewid=48e9af4d%2Dbcef%2D4db2%2Dba07%2Dce3a5b184f21
 
Share the link on the ```ticket```, share the link on the ```Mobile Deployments``` channel and tag the reporter on the ticket, then share link on ```Release Candidates - Android``` channel

![image](https://user-images.githubusercontent.com/43546652/155631918-e9dc1113-d272-4ed6-836e-8f45dbb7615c.png)

You are done with that build, same thing applies if you're to make any other build.


## Adding a New QA Endpoint
Here we'll be using QA3 endpoint as an example, 
Sample ticket: https://mypinpad.atlassian.net/browse/OMP-2714
Sample PR: https://mypinpad.visualstudio.com/Goodfellow/_git/android-terminal-app/pullrequest/10755?_a=files

To add a new Endpoint, first confirm the ticket requirements, then proceed to ```Environment Confluence page``` 
Here is the confluence page for QA3 (https://mypinpad.atlassian.net/wiki/spaces/OPS/pages/996704324/qa-3)

Using the information on the conflience page we can generate this code just following the previous pattern

<img width="516" alt="image" src="https://user-images.githubusercontent.com/43546652/155634073-d7679840-3151-49b2-9b0f-23958bf88db2.png">

The only thing that'll not be provided will be ```trusted-app: => dependency:``` you have to generate it from android-ta-registry repo (https://dev.azure.com/mypinpad/SoftPOS/_git/android-ta-registry)

When you're on the repo, click on Pipelines and select Pipelines from the dialog, 

On the list of Pipelines, select ```sbs - TA v3```

<img width="885" alt="Screenshot 2022-02-25 at 2 25 40 AM" src="https://user-images.githubusercontent.com/43546652/155636076-682290a4-90b4-409d-96fa-fe1f151b2f89.png">

On the list of Pipelines, select ```sbs - TA v3```, then click on ```Run pipeline```

<img width="370" alt="image" src="https://user-images.githubusercontent.com/43546652/155636634-4b0206d0-dfa9-4d8a-a7fa-37df4703c654.png">
Were we have the highlight fields, you replace it with the code below, which are all from QA3 confluence page

```
- buildServiceUrl: build-eu.global.vger.mypinpad.services
  stackName: qa-3
  category: nonlive
  stackHost: terminal.qa-3.eu.dev.vger.mypinpad.services
  taId: b7389f36-2376-47e8-02b8-08d9f31e5a64
```

Once the build is successful, go back to the android-ta-registry repo, then open nonelive, 

<img width="1108" alt="Screenshot 2022-02-25 at 2 35 23 AM" src="https://user-images.githubusercontent.com/43546652/155637006-74030c79-6262-46db-bcc0-3fe287cea1ab.png">

Then locate qa3 folder, you should now see the generated files, the dependency is what we want, copy the dependency to the project

<img width="643" alt="image" src="https://user-images.githubusercontent.com/43546652/155637191-0f30b2c9-4deb-4c67-9ab6-909766e0aea3.png">

<img width="1108" alt="Screenshot 2022-02-25 at 2 35 23 AM" src="https://user-images.githubusercontent.com/43546652/155637006-74030c79-6262-46db-bcc0-3fe287cea1ab.png">

