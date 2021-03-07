## Visualizing Keyword Frequency on Tech Job Listings

Here's a quick code doodle I created a few months ago, combining some topics we'd been covering in both the career services and the Javascript / DOM manipulation classes at General Assembly (I'm currently enrolled in their Software Engineering Immersive program). I found myself wanting to quantify the types of words that appeared across various listings, and rather than simply rely on my unreliable impression as I clicked through, I decided to put on my coding-cap and built something!

%[https://youtube.com/watch?v=tPWfMAeqe8Q]

The process is quite straightforward: 
- copy-paste job descriptions from various listings on [BuiltInColorado](https://www.builtincolorado.com/), [LinkedIn](https://www.linkedin.com/in/benhammondmusic/), and [Indeed](https://www.indeed.com/) into one giant string (this could certainly be improved by scraping automatically with Selenium, similar to my  [GigUploader](https://github.com/benhammondmusic/songkick-bulk-upload)  project)
- strip the string of problematic chars
- remove matches from a persistent array of ignored words ('the', 'a', etc.) 
- print each word to the screen, growing the font-size in proportion to the word frequency.
- use some css to rotate the words and add an interesting perspective effect
- make the number indicator on each word bubble into a button which removes that word from the page and also adds it to the persistent array. For now these words are saved in local storage, but this could be hooked up into a simple MongoDB or other NoSQL database for more meaningful results over time.   

That's it! If you'd like to built on this idea, check out the  [GitHub repo](https://github.com/benhammondmusic/resume/tree/master/wordcloud) . And if it wasn't clear, I will be seeking a role as a software engineering in Denver or remote, starting in May of 2021; if you know of any opportunities for an enthusiastic Full Stack / React dev with 15 years experience running his own tech-forward music company, please refer me! 

