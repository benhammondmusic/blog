## Don't Change the Capitalization of Your Filenames!

This problem popped up for me on my first ever time deploying to Heroku: [Family Friendly](https://benhammond-familyfriendly.herokuapp.com), an Express/EJS app mapping the availability of baby-changing tables for caretakers of all genders. What a first experience... hours and hours of endlessly debugging and messing with settings I had no real idea about (maybe it needs a different version of Node? maybe it's missing a config variable? maybe Microsoft is sabotaging me as retribution for all the nasty things I've said about Internet Explorer in the past?!?!) Thankfully, my instructor [Adonis](adonismartin.com) had encountered this issue before and set me on the right course towards fixing it. 

### The Problem

The problem occurs when a file that is already git-committed is renamed, but only with changes to its capitalization. These changes are remembered by macOS, but *not* by git, and therefor those filename changes will not be pushed to GitHub, Heroku, or any other remote deployments you've set up for your .git repo. However, changes to filename references _in your code_ will be committed and pushed, causing the error. Confusingly, the app will work great on your local machine, so this makes debugging the error all the more difficult.

In my specific case, I had used a model for a report card. As is best practice, I capitalized the Model name, and also followed Javascript's camelCasing standard for multiple word variable names, and used the filename ```ReportCard.js```. Later, I had a change of heart, and decided a report-card was more easily considered a single term, and changed the model name and all references to it in my code to be ```Reportcard.js```. In my code, this occured several places but one example was in my ```models/index.js``` file, which imports all my Models and exports them to a single reference point: 
```
module.exports = {
  User: require('./User'),
  Place: require('./Place'),
  Reportcard: require('./Reportcard'),
};
```
So now, *locally* I have that code working as it's looking for and finding ```Reportcard.js```. However, since it was initially committed to git as ```ReportCard.js```, the module.exports was breaking during the deployment. 

### The Problem, Again

This exact issue occurred a second time in this project, and again months later in my current team project where we are building a MERN stack gig marketplace. In this instance, we had incorrectly named our index model ```Index.js``` (following the idea of Model filenames being capitalized). This is incorrect however, as a capitalized index will not function as the default file for a folder reference, it needs to be the lowercase ```index.js```.  
```
// works when  models index is '../models/index.js' but not if it's '../models/Index.js'

// imports models for Reportcard, Place, User, etc
const db = require('../models');
```

### Even My Code Heroes Have This Problem

%[https://twitter.com/wesbos/status/1361476942973788162]

### The Solution

Don't change the capitalization of your files once they are git committed! 

### The Easy Fix (thanks to  [Wes Bos](https://wesbos.com/) )

```
git mv wrongCapitalization.jsx CorrectCapitalization.jsx
```

###  The Harder Way To Fix IT That I Figured Out

Change the offending file(s) to a totally new filename by prepending some nonsense to the filename, commit and push, then change file back to correct name, commit, and push again. In my case, it was ```models/Reportcard.js``` locally and in my code references, but still ```models/ReportCard.js``` on the deployed remotes.

1.
``` 
# rename to a completely new nonsense filename
mv Reportcard.js beniscoolReportcard.js
```

2.
```
# git commit weird file, and importantly remove the old filename from your commit
git add .
git commit -m "first step fix uppercase bug"
```
3.
```
# deploy with weird file (this assumes my default branch is main and my remote deploy is setup already to Heroku)
git push heroku main
```

4.
```
# change filename back to what you actually want (be sure it matches what's in your code)
mv beniscoolReportcard.js Reportcard.js
```

5.
```
# git commit correct file, and importantly remove weird filename
git add .
git commit -m "last step fix uppercase bug"
```

6.
```
# deploy with correct filename, and it should work, besides all those new errors you'll get now that this one is squashed!
git push heroku main
```


