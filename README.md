
# Google Analytics Queries via Powershell

The goal of this project is to automate the query of google analytics data so that our data warehousing software (we use Wherescape Red) can import it. 

Our institution uses Google Analytics to track web traffic on our learning management system (Canvas). By analyzing this data we are able to ask a wide variety of question related to who is accessing online course content, what content they are accessing and to what extent they are accessing it. 

Though the reporting interface of google analytics is very powerful, we knew we needed to bring the data into our warehouse. The real power of warehousing is the integration of different data sources. Our first objectives are to determine to what extent instructors using the LMS, and to create early-term predictors of student retention and success. 

There are many examples online of the types of reports that one might run directly in google analytics to answer these questions, however, the real power comes from integrating the canvas course ID and User ID that you can feed through GA to your internal student information system (SIS). We are doing this through our data warehouse. By linking these two data sources we can evaluate course engagement by user (student or instructor) by department, course, or each individual. 

Having no background in powershell, and having not worked with many API’s it took me a few days of muddling to get it to work. I hope I can save you some time if you are attempting to access your GA data through Powershell. 


#### Google Analytics Configuration
If you haven’t yet, you need to configure GA to collect data on your webpage. For us, it was the institution’s Canvas system. Note: GA will only collect data after you turn it on - there is no going back in history. 

If you were not the one to configure GA, you need to be granted administrator privileges to the GA account. Note: only google accounts are allowed access, so hopefully you have one to use. 

You will need your google analytics View ID, which should look something like `123456789`.  Not to be confused with your Tracking ID which is in the format `UA-123456789-1`. 

#### Developers Configuration
Head on over to https://console.developers.google.com/ 

There is a drop down menu in the top bar that should say something like “My Project”. Click this and a popup window should appear. Click the `+` on the top right to create a new project. Give your project a name and click `create`. Your project should now appear in the project drop down in the top menu. We’ll come back to that later. 

<img src="/images/project_name.png" width="250">

Looking at the empty dashboard we need to choose which API we want to interact with. Choose the Library option in the left hand menu to search available APIs (google has just a few available). 

Image

Unless they have changed something since this writing, there are two “Analytics APIs” available. Either one should work, but I chose the “Analytics API” over the “Google Analytics Reporting API”. We’ll have to match that up with our settings in the OAuth playground. 

Click `Enable` after which you will be redirected back to the dashboard.
Navigate to the `Credentials` on the left hand menu. Choose `Create Credentials` and choose `OAuth Client ID` then  `Web Application` (I know it’s not a web app, but we are going to pretend to be one) then give it a name (doesn’t matter what it is). 

Importantly we want to create two Authorized Redirect URI’s
http://localhost/oauth2callback (we’ll use this in our script)
https://developers.google.com/oauthplayground (we’ll use this to get our tokens)

Before you save and exit, go ahead and copy your `Client ID` and `Client Secret` into your powershell script (or a text editor for now). 

#### Token Time!

Navigate to https://developers.google.com/oauthplayground/ (note: there are other ways to get your token, this is just the one I used). 

**Step 1:** Authorize and Select APIs
Here you are going to choose the API you chose above and click `Authorize APIs`. You should be redirected to an authorization page. 

Image 

**Step 2:** After authorizing you are given the option to `Exchange Authorization Code` for Tokens. Do that. Copy your access and refresh tokens into your powershell script (or a text editor for now). 

If this authorization doesn’t work, you may need to change some configuration settings in the OAuth 2.0 Playground. This is what mine looked like:

#### Powershell Time
Every windows machine comes with Powershell, you likely just have never used it. Look for your Powershell ISE and Run As Administrator

If you have ever run R, the powershell interface should look familiar. You have a script writing area, and a console. You can type and save commands in the script, or just run commands in the console. The console is where any results set to display will show. Note, the script files save as `.ps1` and can be executed without opening powershell. Because of that, email clients don’t like sending them as attachments (as I learned the hard way). If you want to save and share your script, save it as a `.txt` file. You can always copy it back into Powershell. 

Jump on over to my powershell script, fork it, clone it, copy/paste it and follow along. 

