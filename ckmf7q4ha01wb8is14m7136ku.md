# FamilyFriendly App (Node with Express)

*This post was written back in the days where Heroku provided free hosting; do not expect any example projects or links to Heroku to work.*

> Delegates and restricts users' CRUD permissions with OAuth2; uses JavaScript Map API to plot user location and data points; integrates a deployed MongoDB Atlas NoSQL database; internally operates on a RESTful API backend in Node and Express with EJS templating

### BACKGROUND 

Every parent and caregiver is far too familiar with the drama of diaper changing on-the-go. My family will seek out Starbucks while on road trips, knowing they will provide: changing tables accessible by caregivers regardless of gender, assorted snacks, a standard of cleanliness, and of course coffee for us sleep deprived adults.

My wife suggested building an app that would allow an overstressed parent to quickly look and see whether a rest stop option included those basic childcare amenities, and thus Family Friendly was born! As a simple MVP (minimum viable product), the app currently plots a map showing spots that feature a changing table, and what type of restroom it's in (👩 Womens, Both, etc). Users can view this data based on their geolocation, and if they choose to securely log in with their Google account, they can add their own places and report when they encounter (or don't) the unicorn that is changing tables in BOTH bathrooms.

<iframe src="https://giphy.com/embed/OKqr7RYFNaZZC" width="480" height="356" frameBorder="0" class="giphy-embed" allowFullScreen></iframe>

### SCREENSHOTS

##### Mobile
<table>
<tr>
<td> 
![mobile-splash.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1616090019379/p5SdudU7S.jpeg)
</td>
<td>
![mobile-reports.jpg](https://cdn.hashnode.com/res/hashnode/image/upload/v1616090036999/D9GLduEhI.jpeg)
</td>
</tr>
</table>

##### Desktop (Logged In Google OAuth)

![desktop-list.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1616090069981/ngU9YFMOc.png)

### TECH

- Front-End: HTML, CSS, Javascript, EJS
- Back-End: Node.js (Express, Passport), MongoDB Atlas, Mongoose
- APIs: Google OAuth2, Google Maps Javascript API
- Development: Git, GitHub, VSCode, Prettier, Draw.io, Trello

### RESOURCES

- [Water.css](https://watercss.kognise.dev/)
- [Material Design Icons](http://google.github.io/material-design-icons/)
- [Styling HTML Buttons](https://fdossena.com/?p=html5cool/buttons/i.frag)
- [Forcing HTTPS with Express / Heroku](https://jaketrent.com/post/https-redirect-node-heroku)
- [PNG Tree](pngtree.com) and [Toptal](https://www.toptal.com/designers/subtlepatterns/) - Royalty Free Images

### DEVELOPMENT

![ERD - Entity Relationship Diagram](https://raw.githubusercontent.com/benhammondmusic/familyfriendly/9885dc12ae44f838d799735b6b94fab6502df8aa/docs/erd.drawio.svg)

![Initial Wireframe](https://cdn.hashnode.com/res/hashnode/image/upload/v1616090137711/jUO6uQfXp.jpeg)


### POTENTIAL FURTHER DEVELOPMENT

- Choose an existing place (from Google Places API?) for user to report.
- Add meta tags etc to html.
- Display city and country name in list of places.
- Save a user's last known location, and default to that. Only use geolocation once or when user requests an update.
- More report options: Parking, Seating, Breastfeeding Room, Play Area, Stroller Friendly, etc.
- Filter mapped results based on caregiver and child needs.

### GETTING STARTED

Please enable geolocation when prompted, and be sure the web address begins with HTTPS (this should be done automatically in Node.js now). Development was done in Brave (a Chromium browser).

- View [GitHub Repo](https://github.com/benhammondmusic/familyfriendly)