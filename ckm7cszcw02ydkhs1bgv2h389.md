# Jr Dev Flashcards

*This post was written back in the days where Heroku provided free hosting; do not expect any example projects or links to Heroku to work.*

%[https://www.youtube.com/watch?v=9PfeSBfwitk&ab_channel=BenHammond]

The most effective projects in my experience are those which can *teach me new coding concepts* while simultaneously *solving unique problems*. Rather than simply tossing together yet another To-Do app (just use  [Todoist](https://todoist.com/)  and be done with it), I attempt to find something that needs fixing, identify a way to solve that issue with code, and then I start building. Even better, coding for myself (to start) allows for low-stakes exploration of new tools, techniques and collaborations. 

## Why I Built It

As I am currently enrolled in General Assembly's Software Engineering Immersive, I have been absorbing hours and hours of information, both from the course itself and also supplementally through blog posts, books, podcasts, and chats with fellow devs. Due to early shady breaches of privacy, I had purposely avoided creating a LinkedIn profile, however all signs pointed towards the network being and increasingly essential component of a job-seeker's online brand. Their recently 
 introduced skill assessments aim to allow applicants to demonstrate real knowledge in a field; a simple way for someone to "prove it". I have yet to attempt the skill assessments, and in an effort to prepare as thoroughly as possible, I googled around for some existing flashcard solutions. I found a few, but more interestingly found  [an entire GitHub repo](https://github.com/Ebazhanov/linkedin-skill-assessments-quizzes)  of problems perfect for my practice needs. I decided to put my newly acquired React skills to the test and build myself a web app capable of displaying flippable cards on both desktop and mobile, that would allow my to prepare for the assessments.


![desktop.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1615617111707/eXcCFAJfU.png)

## How I Built It

### Collecting and Parsing the Data

- I had already found the  [above-mentioned repo](https://github.com/Ebazhanov/linkedin-skill-assessments-quizzes)  containing markdown files full of answered multiple choice questions. I selected 4 topics to start: HTML, CSS, JavaScript, and Git, and saved the markdown files to my computer.
- At this point I had to solve what seemed like the simplest task, yet had never needed to do with Javascript: reading in a text file. I have imported .js files, and fetched JSON from APIs, but never needed to simply grab a giant string from a .md file. Some quick googling revealed 
fs as my new friend; a simple, built-in way for JavaScript / Node to read in files.
```const fs = require('fs')```
- Some string manipulation 101 (putting those CodeWars skills to work!) had me slicing and dicing the .md file into usable chunks. I decided the schema of each problem would include: the topic, the question, the multiple choices, and the answer (multiple choices with correct choice selected, as was present in the .md file).
- I now had the data in a useable array of problem objects; next step was creating some API endpoints in Express to serve up those data in various ways.

### Creating an API to Serve the Data
- Unfortunately there isn't a fancy create-express-app, however you can set one up from scratch with only a few lines of code and some npm i commands.
- Get in the habit of creating a ```.gitignore``` file on the floor of your project, and typing ```node_modules``` into it and then saving. This will prevent Git and GitHub from indexing and more importantly uploading/downloading 3rd party node packages, keeping your file sizes smaller and load times much quicker. You can also use this file to identify other files that might contain sensitive information like login information or API keys, and keep Git from indexing those as well; each ignored file goes on a new line inside the .gitignore file. 
- Express provides very simple routing to allow serving of different data based on the relevant endpoint. I decided future-me might need the following data from the frontend: an array of topics, an array of problems from a selected topic, and a specific problem from a selected topic. The last endpoint hasn't been used yet, but it might come in handy down the line. 
- I'll explain some of the techniques I used to serve all of the problems from a selected topic. Code snippet below:

```
app.get('/api/topics/:topicStr/', (req, res) => {
  try {
    if (topics.indexOf(req.params.topicStr) === -1) {
      let msg = 'topic not found';
      throw new Error(msg);
    }
    const data = fs.readFileSync(`./db/${req.params.topicStr}-quiz.md`, 'utf8').toString();
    let allQA = parseMdFile(data);
    res.status(200);
    res.send(allQA);
  } catch (e) {
    res.status(400);
    console.log('**Error**:', e.stack);
    res.send(e.stack);
  }
});
``` 
- Now the backend can obtain a value from the client's selected endpoint; in this case the 
```:topicStr``` represents any string of characters and/or digits occurring after ```/api/topics/```. For example, if Node is running this file locally on port 3001 and I visit ```http://localhost:3001/api/css``` in my browser, the backend will now have access to the client's selected topic of _css_ by using ```req.params.topicStr```. So now I have the target destinations setup for my future frontend client to send HTTP requests to.
- Everything to this point has run entirely on my computer; it would work fine if I turned the wifi off on my computer. I use Node to create a local server running on my computer at a specific port, and instead of a browser interpreting a ```<script>```, Node does the interpreting and acts the same way a remote server will. In my express app I am using *server.js* as my main program file, so to get that running locally I use the command line (terminal, etc) and run a package I've installed called nodemon. 

``` nodemon server.js```

![start nodemon.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1615612285469/kY7pJi51b.png)

- This runs Node but also refreshes the page automatically every time you change your file and save. This is MUCH easier than simply running it with the node command alone: ```node server.js```, and having to kill the process and restart every time you change your code. However, it's important to note that unseen, confusing and unpredictable errors occur from time to time. If you find yourself really scratching your head as to why your code isn't working and you've tried the first couple obvious fixes, it's certainly worth killing the nodemon process in the command line and restarting it. You can tell it's running because you won't see your normal prompt. To kill the process, type ```CONTROL C``` (please note, it is not COMMAND C like you would use to copy to the clipboard). Once the process is killed, terminal will show your normal prompt, in my case the current directory, the current git branch, and the $ symbol. 

![kill nodemon.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1615612421958/dgOT5n38T.png)


### Deploying the API to Heroku

- So obviously you can't leave your laptop running node all day, and allow whatever client app needs to access your API to ping in and mess around with your computer. Luckily, there are multiple *free* options for getting your backend server up and running relatively quickly. I won't say simply, because there are many things that can go wrong, particularly when you are new to the process as I am. My first attempt deploying to Heroku was stymied by a small and hard to detect bug where changing the capitalization of a git commited file causes Git (and thereby Heroku) to ignore further changes to the file. I will post about this and the solution another day; short answer is make a totally new file with a totally new name, delete the wrongly capitalized file, commit, and then rename the totally different file to the properly capitalized original file. 
- I won't outline the entire process (there are many  [great guides online](https://devcenter.heroku.com/articles/git) ), but in short to deploy to Heroku, you sign up for an account, download a CLI (command line interface, basically sets of tools you can run on your command line), and add Heroku as a new remote (the same way you might have GitHub set up as a remote for your local .git repository). Then, when you want to push to heroku, you simply use 
```git push heroku main``` ; this is assuming you are using *main* as your default branch instead of the outdated and problematic *master*. 
- You will also need to change the port to which your app connects; when running locally it's fine to hard code a port to say 3001, however when deploying you'll need to read the port value from the environmental variable set by Heroku, which may change but will be automatically set. The easiest way to allow both your local development server and remote production server to function is to set the port to the env value OR a hard coded port like 3001. That way, it'll check for the dynamic env value first, use that when deployed or fall back to your hard coded value when running locally.

```
const port = process.env.PORT || 3001
app.listen(port, () => {
  console.log(`Your backend app listening at http://${url}${port}`);
});
``` 
- Lastly, you will have to account for *CORS* errors, which annoyingly and importantly block unauthorized access to your site from outside code. The prevents (or at least slows down) bots, hackers, and other would-be code bad guys from ruining your day. By installing and using the cors package ```npm i cors```, you can configure your app to allow specific locations to access your API endpoints. I set mine up to allow access to both my localhost:3001 development client and my deployed front end on netlify; once the app is out of development you would want to remove the localhost option. There is likely a way to only allow localhost while in development and not on the production build; I will investigate!
```
/* cors */
const whitelist = ['http://localhost:3000', 'https://jr-dev-flashcards.netlify.app'];
var corsOptions = {
  optionsSuccessStatus: 200,
  origin: function (origin, callback) {
    if (whitelist.indexOf(origin) !== -1) {
      callback(null, true);
    } else {
      callback(new Error('Not allowed by CORS'));
    }
  },
};
```
- We now have a deployed backend, with API endpoints that can be accessed by specific frontend clients.

### Creating a React App
- Using ```npx create-react-app``` to kick start any react app is quite satisfying; it works and you get a pretty looking page with a spinny logo. Simply delete everything inside the outer <div> inside the return ( ) and then you'll have a blank React canvas to work on.
```return 
   (
      <div> {/* delete this stuff */} </div>
   )
```
- I am quite new at React, however I can say with some certainty if you are also new it's worth writing your new code using functional components and hooks. In this app, I have made two of my own components: <FlashCard /> to represent each flippable card, and <TopicSelect /> to be my select dropdown which changes the current topic. 
- Using hooks allows you to utilize the useState() and useEffect() hooks. In this project, my app.js uses state to keep track of: availableTopics[] which is an array of strings, currentTopic which is a string, and currentDeck[] which is an array of objects containing problem info from the current topic. The useState() hook provides you with the variable, and also a function to set that's variables value. In my case, ```currentDeck[]``` and ```setCurrentDeck()``` as one example. It is standard practice to have your setter function named as the word ``set`` plus your capitalized ```VariableName```. 
- the useEffect() hook is confusing and I am still wrapping my brain around it. However, you must first understand "side effects", or things that your functions do to information outside of the function. Changing the website or DOM is one example. It is helpful for me to keep in mind that is my code is acting weird, it's likely because of a side effect that I should separate out into it's own useEffect(). This hook is also helpful for running a function once on page load, or running a particular function every time a certain value changes. Like dominoes, you have to trace this action causes this actions causes this action, and then line them up in useEffect()s. In this app, the user selecting a new topic in the dropdown changes ```currentTopic``` in state. This in turn causes an API call to update the current deck of flashcards for the new topic. Lastly, this updates the DOM by refreshing all the currently displayed cards with new ones.
- I had kept hearing about Tailwind on [Twitter](https://twitter.com/benhammondmusic) and elsewhere, as being an increasingly popular way to apply CSS. Like Bootstrap it uses utility classes to apply CSS directly to your HTML elements, without having to even type into your CSS file. Unlike Bootstrap, it doesn't have opinionated defaults, and I haven't had to type a single !important override to get the design I wanted. Granted my design is extremely simple and as my wife would say, "not exactly pretty", but it's easy to apply, quick to change, and gives an wide but discrete number of default settings to get you started. I like it so far and plan to use it again in another project.
- To create the card flip effect, I simply ```import ReactCardFlip from 'react-card-flip'``` and follow their instructions.I also found  [a great blog post](https://iuliia-proskurnina.medium.com/how-to-integrate-flip-cards-into-react-app-eab089c4df34)  which showed an updated implementation using state and functional components, and I'd recommend following her guide. Basically your app will place your front and back components inside their ReactCardFlip component. The docs explicitly say to include the "front" and "back" keys, however neither the doc example nor the blog implementation did. I included them because it didn't seem to break it, and maybe it's helping.*shrug*. You also need to pass the Boolean isFlipped down as a prop from ReactCardFlip to your child front and back components (the  ```isFlipped={isFlipped}``` attribute on ReactCardFlip)
```
<ReactCardFlip isFlipped={isFlipped}>
   <CardFront key="front" >this is the front</CardFront>
   <CardBack key="back" >this is the back</CardBack>
</ReactCardFlip>
```

### Deploying the React App to Netlify
- In the same way we don't want to just be able to run our server at home, we also might enjoy being to use this React app on any browser with an internet connection. I have heard a lot about Netlify as a hosting service (mainly because they've been a sponsor of  [Syntax.fm](https://syntax.fm/) ) and decided to try it out. I'm still unsure if I could have deployed this front end to the same Heroku project I am using for my backend API; I also know I could used GitHub Pages to deploy this client as I did that for my  [Tanks!](https://benhammondmusic.github.io/tanks/)  JavaScript game. However, I've heard great things about Netlify, and decided to give it a try.
- It was a very painless process to set up my *free* account, and I had things deployed relatively quickly. There was a learning curve for me however; I realized I had only ever run my React projects locally in development, and to deploy an app you need to actually build the app first and then deploy that. 
- I found the [Netlify Guide to Deploy React Apps in less than 30 Seconds](https://www.netlify.com/blog/2016/07/22/deploy-react-apps-in-less-than-30-seconds/) which looked promising. It was dated 2016 which was less promising, however everything worked the first time with no issues. Be sure to type in ```build``` when it asks for your deploy folder.
- Now we have a build, we can use ```netlify deploy``` to, you guessed it, deploy to netlify. Well actually it deploys to a draft location which is nice because you can double check it before pushing onto your real site. Once you have confirmed it's good to go, you run the same command but with the a flag: ```netlify deploy --prod```

### Actually Quizzing Yourself on Jr. Developer Topics
- Yay! Hooray! We have a functioning app, and it only took use a few days of googling, stack overflowing, debugging, creating and ultimately abandoning feature branches, and coffee consuming. Now, we can flip those flash cards and study up for our next job interview!
- Please comment, submit an issue, create a pull request, or reach out if you have any ideas, thoughts or corrections. I am learning this as I go, and typing these thoughts out to solidify the ideas. If it can help one other person I'd be elated; please let me know!

### GitHub Repos

- [Frontend Repo](https://github.com/benhammondmusic/jr-dev-flashcards-client)
- [Backend Repo](https://github.com/benhammondmusic/flashcards)

### Deployments

- Out of service until I migrate the backend API away from Heroku
