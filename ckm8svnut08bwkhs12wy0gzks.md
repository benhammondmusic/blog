## Deploying Our Team Project Backend to Heroku

A quick guide to assist our project team in getting our group project deployed to each member's individual Heroku app

## These were the issues causing failed deploys:

- the .git repo and the package.json need to both be on the floor of your project. I copied all the files out of the inner folder using ```cp -r /gigboard-Api/. /```, and then deleted the inner folder. Command courtesy of [this Stack Overflow](https://stackoverflow.com/questions/20192070/how-to-move-all-files-including-hidden-files-into-parent-directory-via)
- the PORT needed to be assigned dynamically: Instead of PORT = 3001, we needed to use ```const PORT = process.env.PORT || 3001``` which lets Heroku choose the correct port, and our local machine to use 3001.
- the Index.js model needed to be lowercase index.js to be able to reference it elsewhere by using ./Models/ ... it doesn't seem to recognize the uppercase Index as being the default file in that folder. I needed to fix using the weird capitalization trick, naming it a 3rd weird name, committing, and then changing back to the proper capitalization and committing
- we needed to merge develop into main. I did that on GitHub, but that will need to be done again every time we want the new changes reflected on the deployed web app. 

### To deploy the backend to your Heroku

_in browser_

- visit [https://dashboard.heroku.com/apps] and login if needed
- click dropdown "new" and then "create new app"
- choose an available name that no one else on heroku has used; i used "ben-gigboard" and then click "create app"
- click "deploy" option in the nav bar
- mostly follow the [Deploy using Heroku Git] section, but I'll spell out the changes below.

_in your command line_

- navigate to your backend folder, and make sure you're on your main branch.
- ```git pull```
- ```heroku login```, press any button and then log in with the browser that pops up, then close that browser window
- ```heroku git:remote -a ben-gigboard``` and replace ```ben-gigboard``` with whatever the exact name of your Heroku app is
- ```git add .```
- ```git commit -am "make it work please"```
- ```git push heroku main```
- cross your fingers and wait until you hopefully see ```remote: Verifying deploy... done```
- type ```heroku open``` to launch that sweet backend in your browser, and you should see "hello world" displayed
