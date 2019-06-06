# LiveProjects
Web Development Projects

Over the last three months at the Tech Academy, I worked on a team of fellow junior developers as a fullstack website developer.  With a total of 5 different two-week sprints, I gained a lot of confidence in working in both Django and ASP.NET frameworks.
In the first sprints, I helped create and improve various features of a django website that fetched and aggregated data based on user-preferences, such as weather alerts, current traffic, sport events, concerts, movies, and podcasts.
In the second sprint, I worked on an admin site for the construction company Erectors, Inc. The purpose of this site was to allow project managers to assign and manage tasks adn task details for each of the employees, and allow everyone to chat with each other about related questions.
In the last 3 sprints, I worked on a revamped version of the previous Django Datascraper, one sprint on Frontend, Backend, and Fullstack each.
## Sprints
* [First Sprint (Python full stack)](#first-sprint-(python-full-stack))
* [Second Sprint C# full stack](#second-sprint-C#-full-stack)
* [Third Sprint (Python Front-end only)](#third-sprint-(python-front-end-only))
* [Fourth Sprint (Python Back-end only):](#fourth-sprint-(python-back-end-only))
* [Fifth Sprint (Python full stack):](#fifth-sprint-(python-full-stack))


### First Sprint (Python full stack)
1. Home Page. I added to the homepage a grid of photos representing the different features of the website, which when clicked, directed the user to the latest updates regarding that category, and displayed a error-modal if access is attempted to a page without the user being logged in.  Here is one row of the grid:
```
<h1>Welcome to DataScrape!</h1>
    <h3 id="description">Click on any of the images below to see the latest personalized updates.</h3>
    <p id="please-login">Please login so that you can view your personal updates.</p>
    <div id="popup-overlay" onclick="off()">
        <div id="text">Please login so that you can view your personal updates.</div>
    </div>
    <div class="container-fluid" id="imgs">
        <div class="row" id="homepage_row">
            <div class="col-md-3">
                <div class="flex-container">
                    <div class="photo">
                        <a href="{% url 'movies' %}">
                            <img src="/static/img/Homepage/movies.png" alt="pic" class="img-fluid" width="210" height="210">
                            <div class="overlay">
                                <h3 class="overlay-text">Movies</h3>
                            </div>
                        </a>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="flex-container">
                    <div class="photo">
                        <a {% if user.is_authenticated %} href="{% url 'events' %}" {% else %} onclick="please_login()" {% endif %}>
                            <img src="/static/img/Homepage/thunderstorm.jpg" alt="pic" class="img-fluid" width="210" height="210">
                            <div class="overlay">
                                <h3 class="overlay-text">Weather</h3>
                            </div>
                        </a>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                <div class="flex-container">
                    <div class="photo">
                        <a {% if user.is_authenticated %} href="{% url 'events' %}" {% else %} onclick="please_login()" {% endif %}>
                            <img src="/static/img/Homepage/concert.jpg" alt="pic" class="img-fluid" width="210" height="210">
                            <div class="overlay">
                                <h3 class="overlay-text">Events</h3>
                            </div>
                        </a>
                    </div>
                </div>
            </div>
            <div class="col-md-3">
                    <div class="flex-container">
                        <div class="photo">
                            <a href="{% url 'nasa' %}">
                                <img src="/static/img/Homepage/oceanworlds.jpg" alt="pic" class="img-fluid" width="210" height="210">
                                <div class="overlay">
                                    <h3 class="overlay-text">NASA</h3>
                                </div>
                            </a>
                        </div>
                    </div>
                </div>
        </div>
```
2. I worked on populating a dropdown list that would have a list of friends that the user could send messages to.
```
INSERT CODE
```
3. Weather Alerts. Queried the weather API for current weather alerts, including title, times, and areas affected. Parsed the JSON string and saved appropriate parts to variables in a dictionary. I ran into a bug around the method "zip" -- someone had previously defined a variable of this name and had overwritten the Python method of the same name.
```
class WeatherCurrent:

    def __init__ (self, zipCode, state):
        self.zipcode = zipCode
        self.state = state

        url = f'http://api.openweathermap.org/data/2.5/weather?zip={zipCode},us&units=imperial&APPID={weather_data_api}' # pass the user's zip code and our api imported from config.py
        serialized_data = urllib.request.urlopen(url).read()

        weather_data = json.loads(serialized_data) # grab the json data returned from the api call and deserialize it (turn the JSON data into Python objects we can work with)

        self.temp = weather_data['main']['temp'] # assign variables the appropriate data by accessing nested dictionaries. 
        self.humidity = weather_data['main']['humidity']
        self.last_update = datetime.datetime.fromtimestamp(weather_data['dt']) # use the datetime.fromtimestamp() method to return the local datetime

        # This last section is to provide state weather alerts to the user.
        # Make a custom url with the user's state to navigate to the weather alerts site
        urlAlert = "https://alerts.weather.gov/cap/" + state +".php?x=1"
        
        #Get content of webpage
        content = requests.get(urlAlert).content
        #Then parse it using Beautiful Soup
        soup = bs(content, 'html.parser')
        
        #Find title and areadesc tags in HTML
        self.effective_object = soup.find_all('title')
        self.area_object = soup.find_all('cap:areadesc')
        
        #Initialize empty lists which will later hold the text of the various parts of the advisories
        self.titles = []
        self.effective = []
        self.areas = []
        
        #Loop through the beautiful soup objects to extract the text each holds
        for index in range (0, len(self.area_object)):
            if index > 0: #This condition skips over the first title element which is not an advisory
                obj = self.effective_object[index].get_text() #Get's text of title element
                cur = str(obj) #Saves it as a string into a variable
                sliceIndex = cur.find("issued") + 7 #We need to break the text into two, at the word issued. Save the index of the first letter of "issued"
                self.effective.append(cur[sliceIndex:]) #Append the right side of the sliced text into a list
                self.titles.append(cur[:sliceIndex - 8]) #Append the left side of the sliced text into a list
            self.areas.append(self.area_object[index].get_text) #Append the text relating to affected area into a list
        
        #Store the list of alert lists in a zipped variable to be passed to the HTML page    
        self.final = zip(self.titles, self.effective, self.areas)
```
I passing the dictionary of weather alert to to the HTMl and displayed the information nicely formatted. 
```
<h2>Current Alerts</h2>
<!--Iterate through the final attribute of the weather object, creating a list items to compose the full weather alerts.-->
    {% for alert in weather_current.final %} 
	<h3><font color="red">{{ alert.0 }}</font></h3>
	<p><b>Effective: </b>{{ alert.1 }}</p>
	<p><b>Area Affected: </b>{{ alert.2 }}</p>
    {% endfor %}
```
4. Add Movie Images. Retreived the URLs for the top movie titles from the OMDB API, parsed the JSON to get the URLs, and displayed the images in the HTML tabularly in a view.
```
#Query the API for movie images
        #Make a list of url to the API's for each of the 5 top movies
        base_url = "http://www.omdbapi.com/?s="
        end_url = "&apikey=9bb81779&"
        
        self.api_urls = [] #Initialize list that will hold API urls
        
        #Convert the movie name into a query parameter that will be inserted into the url
        for movie in self.movie_list: #Loop through each of the movies in movie_list
            movie = list(movie) #Convert the string into a list
            for index,char in enumerate(movie): #Go through each movie name, find spaces and replace with "%20".
                if char == " ":
                    movie[index] = "%"
                    movie.insert(index + 1, "20")
                    index += 1
            movie = ''.join(movie) #Convert the list back into a string

            cur_url = base_url + movie + end_url #Add each existing part of url to make a complete, ready-to-use url
            self.api_urls.append(cur_url)
        
        #Make list of urls for each movie image to be passed to the render function and then to HTML
        self.image_urls = [] #Initialize list that will hold image urls

        for api_url in self.api_urls: #Loop through each of the API urls
            json_string = urllib.request.urlopen(api_url).read() #Create JSON string
            json_string = json.loads(json_string)
            cur_url = json_string['Search'][0]['Poster'] #Parse string to get image url
            self.image_urls.append(cur_url) #Add image url to list

        zip_movies = zip(self.movie_list, self.image_urls) #Zip the lists into a package
        self.final_movies = list(zip_movies) #convert zip into list
```

6. We had to add a feature to the website that would let the suer see the a list of the top events in his area.  Given parameters of zipcode and city, I queried the ticketmaster API for relevant data by the zipcode, then if this returned nothing, query it again by cityname.  I put the details for the events into lists, and send this dictionary of variables to HTML where it was displayed appropriately. Looking back on this, what I should have done is written an event class, and save the details to instance of this class for each event, and made just one list of these instances...
```
class EventScraper:
    def __init__(self, city, zipCode):

        #Define a function that receives a JSON object, parses it, and stores relevant data in lists which are then returned
        def get_events(raw_events):
            for index in range(0, len(raw_events)):
                
                list_all = raw_events[index]
                
                name = list_all['name'] #Gets name of event
                description = list_all['classifications'][0]['genre']['name'] #Gets genre of event
                
                time = list_all['dates']['start']['localTime'] #Get's time of event
                time = datetime.datetime.strptime(time, "%H:%M:%S") #Uses the datetime module to format nicely
                time = datetime.datetime.strftime(time, "%I:%M %p")
                time = time.lstrip("0")

                date = list_all['dates']['start']['localDate'] #Get's date of event
                date = datetime.datetime.strptime(date, "%Y-%M-%d")#Uses the datetime module to format nicely
                date = datetime.datetime.strftime(date, "%b %d %Y") 

                #Populates the lists with our data
                events.append(name)
                descriptions.append(description)
                times.append(time)
                dates.append(date)
            
            # using zip, we can pass in our lists, and return a list of tuples; zip infers that each index in one list will correspond to the same index in another list (or this is nonsense and I'm totally wrong)
            final_events = zip(dates, times, events, descriptions)
            return final_events

        #Now we begin defining the main function
        self.city = city
        self.zipcode = str(zipCode)
        final_events = ""
        
        #Go to TicketMaster API and pass in zipcode into api url as a query parameter
        mainUrl = "https://app.ticketmaster.com/discovery/v2/events.json?postalCode="
        mainUrl += self.zipcode
        mainUrl += "&apikey=gx2cYqUZdkuQNeTACkp3A8EbY7oNriXe"
        response = urllib.request.urlopen(mainUrl) # Returns a file like object from opening URL
        final = json.loads(response.read()) #Reads object and saves it as a JSON object that can be parsed below
        
        # we'll create new lists to hold each sub-target(s) data
        times = []
        dates = []
        events = []
        descriptions = []
        
        #Find date, time, event name, and description values in JSON object
        try:
            raw_events = final['_embedded']['events'] #Sets up only the relevant portion of the JSON object, two dictionary levels in
            
            # call above function to get data from JSON object
            final_events = get_events(raw_events)
        except: #If there is no relevant data in the JSON object, after searching by zipcode, then search by city name
            try:
                #Same process as above
                mainUrl = "https://app.ticketmaster.com/discovery/v2/events.json?city="
                mainUrl += self.city
                mainUrl += "&apikey=gx2cYqUZdkuQNeTACkp3A8EbY7oNriXe"
                response = urllib.request.urlopen(mainUrl)
                final = json.loads(response.read())
                raw_events = final['_embedded']['events']
                final_events = get_events(raw_events)

            except: #If still not data, tell user that there are no events for his area
                events.append(zipCode) #Put zipcode in place of event (see events_data)
                times.append("") #Fill the rest of the list with no data
                dates.append("")
                descriptions.append("")
                final_events = zip(dates, times, events, descriptions)
                
        self.final_list = list(final_events) #Make a list of the previously-made lists to pass to the HTML

```
7. Change preferences. Auto-populate list of favorite sports teams, and retrieve the user's favorite team.

### Second Sprint C# full stack
1. Redisigning the Navbar. I looked at the existing sister site as the model for the styling. Using jquery to alter the opacity of the navbar upon scrolling.
```
$(window).scroll(function () {
        
	var elem = document.getElementById("navbar"); //Get's navbar Id
	if (($(this)).scrollTop() >= 30) { //If distance from top is greater than 30px
	    elem.style.transition = "opacity 0.2s linear 0s"; //Call transition function on opacity
	    elem.style.opacity = 0.7; //Set target opacity
	}
	else if (($(this)).scrollTop() < 30) { //If distance from top is less than 30px
	    elem.style.transition = "opacity 0.2s linear 0s";
	    elem.style.opacity = 1;
	}
});
```
2. Creating a chatbox (front end). Learned about textarea tag in HTML, how to create a partial view layered on an existing view and common/visible on specific pages, the importance of referencing JS/Jquery tags in the right order. I styled the box similarly to the navbar, and made it collapsible.
```
function closeChat() {
    document.getElementById("chatForm").style.display = "none";
};

function openChat() {
    document.getElementById("chatForm").style.display = "block";
};
```
The heading had the name of the person to whom the message was being sent. Then I added a dropdown list from which the user could choose who to send the message to.
```
   <div class="chat-popup" id="chatForm">
        <!-- Modal content-->
        <div class="modal-content" method="post">
            <div class="modal-header">
                <button type="button" id="test" onclick="closeChat()" class="close">&times;</button>
                <h4 class="modal-title">@User.Identity.Name</h4>
            </div>
            <form>
                <div class="modal-body">
                    <ul id="discussion"></ul>
                    <textarea placeholder="Type message..." name="msg" id="message" required></textarea>
                    <input type="hidden" id="displayname" value="@User.Identity.Name" />
                    <button type="submit" class="btn" id="sendmessage">Send</button>
                </div>
            </form>
        </div>
    </div>
```
When the user selected a name, the heading in the chatbox also was updated, with this JavaScript code:
```
function updateName(){
    var name = $('.recipient').find('option:selected').text(); //Get and store the username
    if (name != "Select"){ //Make sure name is not the select option
        document.getElementById("chatUser").innerHTML = name;
    }
}
```
Since the chatbox was supposed to be accesible on different pages, I could only pass a model to it from a controller that was common to the various controllers for the different pages. So I created a base controller from which the others inherited, and in that controller I queried the database for all the site users who would potentially become recipients for the messages.
```
Insert CODE HERE
```
3. Saving the messages to the DB.  On the server side chathub class, I was able to retreive the necessary data and save to the DB.
```
public class ChatHub : Hub //Hub base class provides methods that communicates with signalR connections
    {
        public void Send(string name, string message) //This send method is called from Ajax
        {
            object timestamp = DateTime.Now;
            // Call the addNewMessageToPage method to update clients' message boxes.
            Clients.All.addNewMessageToPage($"{ DateTime.Now.ToString("h:mm tt")} {name}: {message}");
            using (ApplicationDbContext db = new ApplicationDbContext())
            {
                ChatMessage chat = new ChatMessage();
                Guid ChatId = Guid.NewGuid();
                chat.Message = message;
                chat.ChatMessageId = ChatId;
                chat.Date = DateTime.Now;
                string currentUserId = HttpContext.Current.User.Identity.GetUserId(); //Get's ID of current user
                ApplicationUser currentUser = db.Users.FirstOrDefault(x => x.Id == currentUserId); //Gets name of current user from ID
                chat.Sender = currentUser; //Save message-sender to db instance
                //System.Diagnostics.Debug.WriteLine(message);
                db.ChatMessages.Add(chat); //Adds a chatmessage entity to collection representing DB table
                db.SaveChanges(); //Apply changes to DB model to DB itself
            }
        }
    }
```
4. Creating a code-first model for Entity Framework with foreign keys.
5. I made a controller to get list of all schedules from DB. I organized them according to those jobs at the same job site, so all schedules with jobs at the same site were put into a list, and the full list of schedules consisted of a list of these lists.
```
public ActionResult Index()
        {
            List<Schedule> scheduleItems = new List<Schedule>(); //List of job items
            List<List<Schedule>> allJobs = new List<List<Schedule>>(); //List of schedule lists

            var jobs = db.Jobs.Select(item => item).ToList(); //Get list of all jobs, each with a different ID

            foreach(Job item in jobs)
            {
                //Make a list of schedule items that have the same JobSite
                scheduleItems = db.Schedules.Where(x => x.Job.JobId == item.JobId).ToList(); 
                //Add this list to list of Schedules organized by JobSite, only if teh schedule is not null, i.e., has jobs
                if(scheduleItems.Count != 0) allJobs.Add(scheduleItems);
            }

            return View(allJobs);
        }
```
Then I created a view page where this data could be displayed in a tabular form.
```
@for (int i = 0; i < Model.Count; i++)
{
    var scheduleList = Model[i];

<h3 class="schedule-list">Job @(i + 1): @scheduleList[0].Job.JobTitle</h3>

    <table class="table">
        <tr>
            <th>
                Person
            </th>
            <th>
                Work Type
            </th>
            <th>
                Shift Time
            </th>
            <th>
                Note
            </th>
        </tr>
        @foreach (var item in scheduleList)
        {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Person.UserName)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.WorkType)
                </td>
                <td>
                    @item.Date
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Note)
                </td>
                <td>
                    @Html.ActionLink("Edit", "Edit", new { id = item.ScheduleId }) |
                    @Html.ActionLink("Details", "Details", new { id = item.ScheduleId }) |
                    @Html.ActionLink("Delete", "Delete", new { id = item.ScheduleId })
                </td>
            </tr>
        }
    </table>
}
```
6. In order for new employees to make use of the site, they were required to validate a confirmation code sent to them by a message.  I wrote the code that would allow the manager to send this confirmation code to the employee:
```
```

### Third sprint (Python Front-end only)

1. Readded missing login button.
2. I was responsible for making the entire website have a uniform styling in terms of fonts, colors, and spacings. I made plenty use of bootstrap features, specifically rows and columns to organize the different elements in. I was given a palette of theme colors to choose from, and apply them to buttons, backgrounds and text. To do so, I created several CSS variables for these colors that could be easily accessed anywhere in the CSS.
```
:root {
  --text-color:#99a2b5;
  --text-hover: #9AA3A8;
  --navbar-color: #2A373F;
  --background-color: #1b1b21;
  --default-button:#5C6971;
  --color-standard:#28AC60;
  --color-standard-hover: #2FCC71;
  --user-color: #389EAA;
  --user-color-hover: #7AC9D0;
  --text-in-dark: #EBF1F1;
}
```
The different HTML elements made use of these variables, so if ever a slight tweak of the colors were necessary, they could all be changed at one stroke.
3. Added an app manager to the profile page. Basically a bunch of compartments, each containing the app icon, several slider buttons, description, and list of app contributors. The icons and buttons are colored according to the agreed on colors for the apps. (This app manager was updated in a later sprint below; you can see the updated code there.)
4. The news-app was just a static page with the headline and photo for each news article.  I converted this into a carousel in which each entity was the news photo with an overlay of the headlines, and the user could go through each one by clicking forward and backward arrows. Then I added a slider by which, though not fully functional, allowed the the user to navigate through the different headings.
```
<div class="carousel slide" id="news-container">
      <div class="carousel-inner row mx-auto">
        {% for item in news.all_list %}
        {% if item.3 == 0 %} <!--Creates the first newsheading with active attribute-->
          <div class="carousel-item col news-item active">
            <a href={{ item.1 }}>                                           
              <img src={{ item.2 }}>
              <div class="news-overlay">
                <h3 class="news-heading">{{ item.0 }}</h3>
              </div>
            </a>
          </div>
        {% else %} <!--Creates the rest of the news heading-->
          <div class="carousel-item col news-item">
            <a href={{ item.1 }}>                                           
              <img src={{ item.2 }}>
              <div class="news-overlay">
                <h3 class="news-heading">{{ item.0 }}</h3>
              </div>
            </a>
          </div>
        {% endif %}
        {% endfor %}
    </div>
      <a class="carousel-control-prev" id="prev-button" href="#news-container" role="button" data-slide="prev">
          <i class="fa fa-chevron-left fa-lg news-chevron"></i>
          <span class="sr-only">Previous</span>
      </a>
      <a class="carousel-control-next text-faded" id="next-button" href="#news-container" role="button" data-slide="next">
          <i class="fa fa-chevron-right fa-lg news-chevron"></i>
          <span class="sr-only">Next</span>
      </a>
  </div>
  <!--Slider for moving through headings easily-->
  <div class="slider-box">
    <div class="slidecontainer">
      <input type="range" min="1" max="" class="news-slider" value="1" id="news-range">
    </div>
  </div>
.....
@media (min-width: 768px) {

  /* show 3 items */
  .carousel-inner .active,
  .carousel-inner .active + .carousel-item,
  .carousel-inner .active + .carousel-item + .carousel-item  {
    display: inline;
  }

  .carousel-inner .carousel-item.active:not(.carousel-item-right):not(.carousel-item-left),
  .carousel-inner .carousel-item.active:not(.carousel-item-right):not(.carousel-item-left) + .carousel-item,
  .carousel-inner .carousel-item.active:not(.carousel-item-right):not(.carousel-item-left) + .carousel-item + .carousel-item
  {
    transition: none;
  }

  .carousel-inner .carousel-item-next,
  .carousel-inner .carousel-item-prev {
    position: relative;
    transform: translate3d(0, 0, 0);
  }

  .carousel-inner .active.carousel-item + .carousel-item + .carousel-item + .carousel-item + .carousel-item {
    position: absolute;
    top: 0;
    right: -50%;
    z-index: -1;
    visibility: visible;
  }

  /* farthest right hidden item must be absolute position for animations */
  .carousel-inner .carousel-item-prev.carousel-item-right {
    position: absolute;
    top: 0;
    left: 0;
    z-index: -1;
    display: block;
    visibility: visible;
  }

  /* left or forward direction */
  .active.carousel-item-left + .carousel-item-next.carousel-item-left,
  .carousel-item-next.carousel-item-left + .carousel-item,
  .carousel-item-next.carousel-item-left + .carousel-item + .carousel-item
  {
    position: relative;
    transform: translate3d(-100%, 0, 0);
    visibility: visible;
  }

  /* right or prev direction */
  .active.carousel-item-right + .carousel-item-prev.carousel-item-right,
  .carousel-item-prev.carousel-item-right + .carousel-item,
  .carousel-item-prev.carousel-item-right + .carousel-item + .carousel-item
  {
    position: relative;
    transform: translate3d(100%, 0, 0);
    visibility: visible;
  }
}
```
5. Added a default profile image if the user has not selected a personal one, and eliminated an upload button by automatically uploading/changing the picture once the user has selected a profile pic from a file explorer.
```
{% if user.userprofile.image %}
                <img class="profile-image img-fluid float-left" id="prof-pic" src="{{ user.userprofile.image.url }}" width="166" height="141"/>
            {% else %}
                <img class="float-left" id="prof-pic" src="../static/img/blank-profile.png" width="166" height="141" alt="Your Name Here" />
            {% endif %}
.....
$(document).on('change', ':file', function() {
    $("#upload").submit();
});

```

### Fourth Sprint (Python Back-end only)

1. Account App. Goal: allow user to switch a button allowing him to toggle automatic geolocation or not. Added slider to HTML, linked it to AJAX call which would post when the slider was switched from its current position. (In this process, discovered the necessity of installing a function that would create a csrf token for Django's authentication system)
```
function getCookie(name) {
    var cookieValue = null;
    if (document.cookie && document.cookie != '') {
        var cookies = document.cookie.split(';');
        for (var i = 0; i < cookies.length; i++) {
            var cookie = jQuery.trim(cookies[i]);
            // Does this cookie string begin with the name we want?
            if (cookie.substring(0, name.length + 1) == (name + '=')) {
                cookieValue = decodeURIComponent(cookie.substring(name.length + 1));
                break;
            }
        }
    }
    return cookieValue;
}
```
The setting was saved to database.
```
if buttonID == "#geo-button": #If main geolocation button was changed
        print(buttonValue)
        if buttonValue == "True":
            curruser.user_tracking = True
            #Save user's new location
            url='https://api.ipgeolocation.io/ipgeo?apiKey=b19ee562aa72436e94a39580c8265ad3'
            r = requests.get(url).json()
            curruser.city = r['city']
            curruser.lng = float(r['longitude'])
            curruser.lat = float(r['latitude'])
        else:
            curruser.user_tracking = False
        curruser.save()
```
The weather app (the only one at the moment using geolocation) was updated so that it would check the database whether to fetch current location first before finding weather, or use the default location stored in the DB.
```
if curruser.user_tracking == 1 or curruser.city == "":
        cityname = 'https://api.ipgeolocation.io/ipgeo?apiKey=b19ee562aa72436e94a39580c8265ad3'
        r = requests.get(cityname).json()
        city = r['city']
    else:
        city = curruser.city
```
2. Traffic App. Goal: display directions on map. At first I tried getting images of the map by accessing Mapquest's main API, which returns a JSON. However, all the images returned were too small and none showed the directions from start to finish, but only sections. Then I switched to Mapquest's JS API, where I could load real zoomable maps onto the HTML.  I queried the API's portals by giving them the starting and ending locations that the user entered into the text boxes.
```
L.mapquest.key = 'qGyMswGafi2puTNqSP91ETXcNRDFrAyG';

//Hides map until directions have been found
document.getElementById('map').style.width = 0
document.getElementById('map').style.height = 0

//Get text of direction heading
var test = $('#dir-heading').html();

if( test.length > 21) { //Checks if heading text is default length, or if it has directions in it
    
    //Gets directions
    
    function directionsCallback(error, response) {
        var map = L.mapquest.map('map', {
        center: [0,0],
        layers: L.mapquest.tileLayer('map'),
        zoom: 7
    });

    //Adds route direction to map
    L.mapquest.directionsLayer({
        directionsResponse: response
        }).addTo(map);
        return map;
    }

    //Get starting and ending locations entered by user
    var startLoc = $("#id_start").val();
    var endLoc = $("#id_end").val();

    var directions = L.mapquest.directions();
    
    //Returns starting and ending points to API
    directions.route({
        start: startLoc,
        end: endLoc
    });
```
I set up the page so that it would only show the map, and display the directions on the page, after the user had already requested directions from the main API.
```
    //Displays map now that directions have been found
    document.getElementById('map').style.width = "100%"
    document.getElementById('map').style.height = "530px"
    
    var map = L.mapquest.map('map', {
        center: [0, 0],
        layers: L.mapquest.tileLayer('map'),
        zoom: 12
    });
};
```
3. Messaging App. Goal: add response functionality to message app. To do this, I added a button to each message in the inbox, that when clicked on, would display a textbox where the user could respond to the message, as well as a button to send the message.
```
<form class="response-form" id="response-{{ message.id }}" method="post">
	{% csrf_token %}
	<!--These automatically get the recipient name and thread id when the message is posted-->
	<input style="display: none;" name="response-recipient" value="{{ message.recipient }}">
	<input style="display: none;" name="response-thread" value="{{ message.thread }}">
	{{ form.messageBody }}
	<button class="btn default-button message-buttons" type="submit">Send</button>
</form>
```
On the backend, I created another column in the database to keep track of message threads, so that the message couuld eventually be displayed nicely on the page grouped according to the conversation thread (I did not actually implement this part).
```
def inbox(request):
    #If a message is replied to in the inbox
    if request.method == "POST":
        form = MessageForm(request.POST)
        if form.is_valid():
            message = form.save(commit=False)
            message.sender = request.user
            message.recipient = request.POST.get('response-recipient', None)
            message.thread = request.POST.get('response-thread', None)
            message.sent = datetime.now()
            message.save()
        return redirect('inbox')
    #If inbox is loaded
    else:
        messages = Message.objects.filter(Q(sender=request.user) | Q(recipient=request.user)).order_by('-thread')
        form = MessageForm()
        return render(request, 'chat/inbox.html', {'messages': messages, 'form': form})
```
The value of the conversation thread was the message of the id of the first message that was responded to.
4. Movie App. This was an app that would allow the user to view most popular movies, sort them by year and/or genre, and change the number to display on the page. For each movie, there is a toggle button to allow the user to view details on the movie, such as year, genre, director, and plot. To setup, I added a movie class with the properties already mentioned, and added a model/DB table for saving the user preferences for display and sort options, set up the HTML page with appropriate buttons and drop down list. The view consisted of two functions, one to deal with the the get/post requests and another to actually fetch the list of movies. The first function checked the user display preferences, saved any new changes to the DB, and passed these changes to the second class so it could fetch movies accordingly.
```
def topfive(request):
   
    #Object representing the current user's row in DB
	try:
		db = MoviePreferences.objects.get(user_id = request.user.id)
	except: #If user does not have a row in the DB, create one
		dbCreate = MoviePreferences.objects.create(user_id = request.user.id)
		dbCreate.save()
		db = MoviePreferences.objects.get(user_id = request.user.id)
	
	if request.method == "GET":
		return get_movies(request, db.display, db.year_sort, db.genre_sort)
	elif request.method == "POST":
		#If different number of movies to be displayed was changed
		if request.POST.get('displayNumber', None) != None:
			custom_display = int(request.POST.get('displayNumber', None), 10)
			db.display = custom_display #Save value to display column
			db.save()
			return get_movies(request, custom_display, db.year_sort, db.genre_sort)
		#If a year sort was requested
		elif request.POST.get('year-sort', None) != None:
			db.year_sort = int(request.POST.get('year-sort', None), 10)
			db.save()
			return get_movies(request, db.display, db.year_sort, db.genre_sort)
		#If a genre sort was requested
		else:
			if request.POST.get('sort-type', None) != None:
				genreSort = request.POST.get('sort-type', None)
				db.genre_sort = genreSort
				db.save()
				return get_movies(request, db.display, db.year_sort, genreSort)
```
In the second function, using beautiful soup and parsing techniques, I got the top 100 movie title from IMDB. Then for each movie, using the title as parameter, I queried the OMDB API for details for each movie.  This involved installing python's w3lib module to make the title string into a safe parameter with only ASCII characters, to remove the possibility of foreign characters being passed in. The OMDB API returned a JSON object which I parsed for the data I wanted, then saved the appropriate data to the appropriate properties of a movie class instance.  A list of movies was thereby created, and returned in a context to the HTML.
In order to implement the genre search efficiently, before creating a movie instance and adding it to the list, I checked the JSON response to see if the movie included the genre the user requested, and if not, it simply returned to the top of the loop to find the next movie.
To implement the display feature, the loop to fetch movies was ran until the length of the movie list was as long as the desired display.
```
def get_movies(request, displayNum, yearSort, genreSort):
	all_movies = []
	api_beginning = "http://www.omdbapi.com/?apikey=360766ac&t="
	# The following loop will run once for each movie title fetched from the IMDB, until
	# the desired number of movies is fetched, or until the IMDB list is exausted
	counter = 0
	while len(all_movies) < displayNum and counter < 100:
		# get title
		movie_string = movies[counter].get_text()
		movie_title = movie_string.split("(")[0] #Get just movie title	
		
		fixUrlParam(movie_title)

		movie_title_parameter = "".join(movie_title) #Convert back to string (unicode)
		fullURL = api_beginning + movie_title_parameter #Create full URL
		fullURL = fullURL.encode('utf-8') #Converts the string into bytes using utf-8 encoding
		fullURL = safe_url_string(fullURL) #Pass this byte list into w3lib's safe url method, and fix illegal characters	
		
		print(fullURL)

		cur_json = urllib.request.urlopen(fullURL).read() # Fetch a JSON object from API
		cur_json = json.loads(cur_json) #Decode object into dictionary
		
		#Try this next section if there are no errors in reading the JSON,
		# i.e., the movie does not exist in the DB, etc.
		try:
			#Save the movie's details
			title = cur_json['Title']
			year = cur_json['Year']
			runtime = cur_json['Runtime']
			director = cur_json['Director']
			genre = cur_json['Genre']
			plot = cur_json['Plot']
			image = cur_json['Poster']
			index_place = counter
			
			#Year variable must be an int in order for the year sort logic to work. The try/catch statement
			# covers the case where JSON returns a non-int value 
			try:
				year = int(year)
			except:
				if "-" in year:
					year = int(year.split("-")[0])
				else:
					year = 0 # Dummy year of 0 to put this movie at end of list
			
			# If movie's genre matches genre selection, all good
			if genreSort in genre or genreSort == "All":
				pass
			# If non, break the loop and begin a new iteration
			else:
				counter += 1
				continue
			
			# Create instance of Movie class and save the previous movie details
			# to the appropriate properties
			cur_movie = Movie(title, year, runtime, director, plot, genre, image, index_place)

			#Sort by year if this is user's preference
			if yearSort == 1:
				sortMovies(all_movies, cur_movie)
			# Otherwise, jsut add movie to end of movie list
			else:
				all_movies.append(cur_movie)
			
			counter += 1
		
		# Catches potential key error if JSON cannot be read
		except KeyError as e:
			print(e)
			counter += 1

	#Replace any year 0 with "N/A"
	for movie in all_movies:
		if movie.year == 0:
			movie.year = "N/A"
	# Dictionary to be passed to HTML
	context = {'imdb' : all_movies, 'sortBool': yearSort}
	
	return render(request, 'movie/movie.html', context)
```
To implement the year sort, instead of sorting the list of movies by genre after the list was created, I wrote a simple algorithm to insert the new movie at the correct place in the list each time a new movie was fetched, so that by the end the list was alreadt sorted.
```
def sortMovies(totalMovies, currentMovie):
	index = 0
	if len(totalMovies) == 0: #If first movie to be added to list, no sorting necessary
		totalMovies.append(currentMovie)					
	else: #Otherwise, insert movie at appropriate position
		while(index < len(totalMovies) and currentMovie.year < totalMovies[index].year):
			index += 1
		totalMovies.insert(index, currentMovie)
```
In order to make the genre, sort, and display button on the HTMl page communicate with the server, I wrote some JavaScript functions:
```
//Display details for movie that user clicks on
$(".movie-deets").click(function (){
    var id = $(this).attr("id");
    $(this).text($(this).text() == "See Details" ? "Hide Details" : "See Details"); //Toggle button text logic
    id = id.replace("button", "#details") //Get div id
    $(id).toggle(); //Toggle display of this div
});

//Updates page when user selects different number of movies
$(".display-item").on('click', function (){
    var option = $(this).text(); //Get value of selected option
    $("#display").val(option); //Store this value in input tag
    $("#display-num").submit(); //Submit value of input
});

//Updates page when user selects a genre to sort by
$(".sort-item").on('click', function (){
    var option = $(this).attr('id');
    $("#sort").val(option);
    $("#sort-form").submit();
});

//Updates page when user chooses to sort by year
$("#year-sort").on('click', function (){
    if( parseInt($("#sort-input").val()) == 1){
        $("#sort-input").val(0);
    }
    else {
        $("#sort-input").val(1);
    }
    $("#sort-year").submit();
});

//Stops playing youtube video when modal is closed
var src = $('.modal').find('iframe').attr('src'); //Get url for youtube video (needed later)
var close =  $('.modal').find("button"); //Get close button object

close.on('click', function(){ //Function to execute when modal closed
    $('.modal').find('iframe').attr('src', ''); //Removes url source for video, thus closing it
    $('.modal').find('iframe').attr('src', src); //Puts it back right away to be used for next time the modal is opened
});
```
### Fifth Sprint (Python full stack)
1. Recipe App. For the first four days of this sprint, I worked on creating a new app that would allow the user to search for recipe by ingredient name, display the search results to the user, and let the user click on a recipe see the details.
I broke the problem down into two main functions: the first function received the user's search word and searched a recipe API for recipes with that name. The second function returned details on a specific recipe that the user selected.
In order to make the first function return a maximum number of recipes, I implemented two separate queries, one that used the serachword as a paramater for ingredient name, and the other that searched by recipe name. The two results were joined together and duplicates removed, then were returned to the HTML page.
```
def mealsearch(request):
    if(request.method == "GET"):
        return render(request, "Meal/mealsearch.html")
    else:
        searchIngredient = str(request.POST.get('search', None))
        #Replace spaces with URL safe equivalent
        searchIngredient = searchIngredient.replace(" ", "%20")
        all_meals = []
        
        #Search by main ingredient
        ingredient_URL = "https://www.themealdb.com/api/json/v1/1/filter.php?i="
        ingredient_URL += searchIngredient
        search1 = get_json(ingredient_URL)
        jsonList1 = search1['meals']
        
        if jsonList1 != None:
            #Fetch attributes for each meal in list
            for meal in jsonList1:
                id = meal['idMeal']
                title = meal['strMeal']
                image = meal['strMealThumb']

                cur_meal = RecipeBasic(id, title, image)
                all_meals.append(cur_meal)
        
        #Search by meal name
        meal_URL = "https://www.themealdb.com/api/json/v1/1/search.php?s="
        meal_URL += searchIngredient
        search1 = get_json(meal_URL)
        jsonList2 = search1['meals']

        if jsonList2 != None:
            #Go through each meal in JSON object
            for meal in jsonList2:
                counter = 0
                #Check if this object is already in the list (we don't want duplicates)
                for MEAL in all_meals:
                    if MEAL.id == meal['idMeal']:
                        break
                    counter += 1
            #If no duplicates, assign the appropriate data to a recipe instance, and add to existing list
            if counter == len(all_meals):
                id = meal['idMeal']
                title = meal['strMeal']
                image = meal['strMealThumb']

                cur_meal = RecipeBasic(id, title, image)
                all_meals.append(cur_meal)
        
        if len(all_meals) == 0:
            resultBool = False
        else:
            resultBool = True
        #Return list to HTML
        context = {'meals': all_meals, 'results': resultBool, 'category': searchIngredient}
        return render(request, "Meal/mealsearch.html", context)


#Given a URL to an API, returns a JSON object
def get_json(url):
    json_object = urllib.request.urlopen(url).read()
    return json.loads(json_object)
```
Each recipe was displayed with its picture in a scrollable box. Hidden within each recipe display was a link when clicked sent a request to the server to view the details of that recipe. Along with the request was sent the recipe ID (previously sent to the HTML with each recipe image) so that the server could find the details associated with that ID.
```
{% if results == True %}
<div class="flex-container d-flex" id="recipes-box">
  {% for meal in meals %}
    <div class="recipe-display col-sm" id="{{ meal.id }}">
      <img class="recipe-image" src="{{ meal.image }}" width="250px">
      <h4>{{ meal.title }}</h4>
      <form method="GET" id="form-{{ meal.id }}" action="{% url 'recipe' id=meal.id %}"></form>
    </div>
  {% endfor %}
</div>
{% elif results == False %}
<h3>Sorry, there are no recipes for that search.</h3>
{% endif %}
```
With this ID, the second function fetched the details for this recipe. Getting the ingredients in their measurements was a bit tricky, since the API separated out the ingredients each with their own keys and values. And there were always 20 key value pairs for each ingredients and measurements, regardless of how many ingredients were actually used in the my solution was to get each ingredient and corresponding measurement that was not empty, join them together in one string, and then append this resulting string to a list of ingredients.
```
def recipe(request, id):
    #Create URL for API
    meal_URL = "https://www.themealdb.com/api/json/v1/1/lookup.php?i="
    meal_URL += id
    #Get JSON response
    recipeJSON = get_json(meal_URL)
    recipeJSON = recipeJSON['meals'][0]
    #Fetch recipe details
    title = recipeJSON['strMeal']
    area = recipeJSON['strArea']
    image = recipeJSON['strMealThumb']
    instructions = recipeJSON['strInstructions'] 
    youtube = recipeJSON['strYoutube']
    ingreds = []

    #The JSON always has 20 ingredient and keys, and 20 corresponding measurement keys.
    # Now we need to fetch the ones that aren't empty.
    for index in range(1, 20):
        # Get ingredient name
        ingredKey = "strIngredient" + str(index)
        cur_ingred = recipeJSON[ingredKey]
        #If we have an ingredient name, then get the measurement associated with it, assuming it's not null
        if cur_ingred != "" and cur_ingred != "null" and cur_ingred != None:
            #Get measurement
            measureKey = "strMeasure" + str(index)
            cur_measure = recipeJSON[measureKey]
            #Join the ingredient with its measurement. Make sure there is actually a value for the measurement
            if cur_measure != "" and cur_measure != "null" and cur_measure != None:
                complete_ingred = cur_measure + " " + cur_ingred
            else:
                complete_ingred = cur_ingred
            ingreds.append(complete_ingred)

    #Instance of recipe class, with its properties assigned the values previously found
    recipe = FullRecipe(id, title, image, area, instructions, youtube, ingreds)
    
    #Call method to alter URL so it can be emedded
    recipe.embedYouTube()
    #Call function to parse out instructions into separate steps
    recipe.instructions = parseInstructions(instructions)

    context = { "recipe": recipe }
    return render(request, "Meal/recipe.html", context)
```
For the instructions, I had to remove the new line characters in the text and instead break the text in two separate sections corresponding to these brakes. And then I labeled each section with a number so that the list of instructions would be numbered.
Then, once the whole recipe instance was filled out, it could be sent as a context variable to the HTML, where each property be displayed appropriately.
```  
#Modifies yotube URL so that it will be embedded in HTML
def embedYouTube(url):
    if "watch?v=" in url:
        url = url.replace("watch?v=", "embed/")
        url += "?controls=1"

def parseInstructions(instructions):
    #Find newline characters in instruction, and split string into list of strings,
    #previously separated by these characters
    directionList = instructions.split("\r\n")
    finishedDirections = []
    
    counter = 0
    for direction in directionList:
        #Append step number before string
        stepLabel = str(counter + 1) + ".  "
        direction = stepLabel + direction
        #Make sure the current string is not nothing but the step number, i.e.,
        #the string after splitting was another newline character
        if direction != str(counter + 1) + ".  ":
            finishedDirections.append(direction)
            counter += 1
    #Convert previous string property to the list of directions. The benefit of a weakly typed language :)
    return finishedDirections
```
In the HTML, I had to play with different layers to get the recipe image to sit on top of a blurred background image. I also put a button there to all the user to watch an instructional video that would appear in a pop-up modal.
```
<div class="text-center mb-4">
    <a class="btn default-button" href="javascript:history.back()">Back to recipe list</a>
    {% if recipe.youtube %}
    <a class="btn default-button" data-toggle="modal" data-target="#exampleModal" id="video-link">Watch Video</a>
    {% endif %}
</div>
```
2. For the rest of the Sprint, I worked on consolidating and streamlining the code that display the app dashboard and app manager. There were three parts to this:
1) Modifing the Apps' Models. The settings page was comprised mainly of modules that control the settings of each app. Previously, the details about each app as well as it icon were hardcoded in the HTML page for each module. In order to keep better track of the various apps, and make it easier to add and remove apps, I wrote a script that would create a DB table that would store just these details and populate it with data.  In the next step, I queried this data and incorporated it into the app manager.
2) Store user preferences for each app. Eventually we wanted the user to be able to switch on and off certain features of each app at will, and store these features.  After toying with several ideas of database architecture, I decided on separate tables for each app that would hold the app settings for each user. Then, I made one linking table that had foreign keys to that user's row in each of the app preference tables.
```
class AllAppPreferences(models.Model):
    user = models.OneToOneField(User, on_delete=models.CASCADE)
    Bands = models.ForeignKey(BandsPreferences, on_delete=models.CASCADE)
    Budget = models.ForeignKey(BudgetPreferences, on_delete=models.CASCADE)
    Craigslist = models.ForeignKey(CraigslistPreferences, on_delete=models.CASCADE)
    Movies = models.ForeignKey(MoviesPreferences, on_delete=models.CASCADE)
    Habits = models.ForeignKey(HabitsPreferences, on_delete=models.CASCADE)
    Messages = models.ForeignKey(MessagesPreferences, on_delete=models.CASCADE)
    Podcasts = models.ForeignKey(PodcastsPreferences, on_delete=models.CASCADE)
    News = models.ForeignKey(NewsPreferences, on_delete=models.CASCADE)
    Recipes = models.ForeignKey(RecipesPreferences, on_delete=models.CASCADE)
    Restaurants = models.ForeignKey(RestaurantsPreferences, on_delete=models.CASCADE)
    Space = models.ForeignKey(SpacePreferences, on_delete=models.CASCADE)
    Traffic = models.ForeignKey(TrafficPreferences, on_delete=models.CASCADE)
    Weather = models.ForeignKey(WeatherPreferences, on_delete=models.CASCADE)
```
This way, I could access all the user's settings through one model corresponding to this table, just using the user's id as the query parameter.
3) Creating a "Controls Template."  Since the app manager modules were the same in their general layout, but only different in details, I built a template that each module could be built from, and the details could be filled in depending on the specific app.  Since Django does not allow multiple extend tags, I built my own custom Django tag. On the server side it fetched the details for each app and the users preferences for all the buttons on each app, zipped both into a list, and then sent this data to the HTML template.
```
register = template.Library()
#For each app in the app manager on the profile page
class Manager():
    def __init__(self, name, icon, description, contributors, button1, button2, button3, color):
        self.name = name
        self.icon = icon
        self.description = description
        self.contributors = contributors
        self.button1 = button1
        self.button2 = button2
        self.button3 = button3
        self.color = color
#For each app on the homepage dashboard
class HomeApps():
    def __init__(self, title, icon, url):
        self.title = title
        self.icon = icon
        self.url = url

  #This tag is for displaying app details on the app manager
  #Register this function as a custom Django tag. Parameter is the template that the tag will render.
  #Then, we can load this tag with the function name below on any HTML page adn this tag wil be loaded
  @register.inclusion_tag('CustomTags/app_manager.html')
  def display_apps(request):
      #Get list of all apps and their details
      db_all = AppProps.objects.all()
      allApps = []
      #Create instances of Manager with app details for each app
      for app in db_all:
          curApp = Manager(app.app_name, app.icon, app.description, app.contributors, app.button1, app.button2, app.button3, app.color)
          allApps.append(curApp)

    all_apps_preferences = []

    for app in all_apps: #Gets the user's preferences for each app
        #Get list of all preference values associated with current user
        cur_prefs = app.objects.filter(user_id = request.user.id).values_list()
        cur_app = app() #Create instance of current AppPreference

        #Loop through the preference values for each button, assigning the values to the respective properties of the instance
        counter = 0
        for pref in enumerate(cur_prefs[0]):
            if type(pref) != int: #Excludes the userid column
                if counter == 1: #Assign properties to the instance depending on which properties we're on
                    cur_app.offOn = pref[1]
                elif counter == 2:
                    cur_app.button1 = pref[1]
                elif counter == 3:
                    cur_app.button2 = pref[1]
                elif counter == 4:
                    cur_app.button3 = pref[1]
            counter += 1
        all_apps_preferences.append(cur_app) #Add to list of app preference instances

    both_lists = zip(allApps, all_apps_preferences)

    return {'apps': both_lists}
```
On the client side, the data was unpacked, creating as many app modules as there were apps. Depending on the user's preferences, the buttons would be show as either on or off.
```
{% for app, preference in apps %}
<div class="col-sm icon-border">
    <div class="row">
        <div class="col-sm-1">
            <i class="fas {{ app.icon }} fa-5x"></i>
        </div>
        <div class="col-sm d-block pr-3 text-right">
            <div>
            <h4 class="inline-{{ app.color }}">Off-On </h4><label class="switch">
                <input type="checkbox" class="pref-button" id="{{ app.name }}offOn" {% if preference.offOn == True %} checked {% endif %}>
                <span class="slider slider-{{ app.color }} round"></span>
            </label>
            </div>
            <div>
                <h4 class="inline-{{ app.color }}">{{ app.button1 }}</h4><label class="switch">
                    <input type="checkbox" class="pref-button" id="{{ app.name }}button1" {% if preference.button1 == True %} checked {% endif %}>
                    <span class="slider slider-{{ app.color }} round"></span>
                </label>
            </div>
        </div>
    </div>
    <div>
        <p>{{ app.description }}</p>
        <div class="text-right">
            <p class="inline-{{ app.color }}">{{ app.button2 }} </p><label class="switch">
                <input type="checkbox" class="pref-button" id="{{ app.name }}button2" {% if preference.button2 == True %} checked {% endif %}>
                <span class="slider slider-{{ app.color }} round"></span>
            </label>
        </div>
        <div class="text-right">
            <p class="inline-{{ app.color }}">{{ app.button3 }} </p><label class="switch">
                <input type="checkbox" class="pref-button" id="{{ app.name }}button3" {% if preference.button3 == True %} checked {% endif %}>
                <span class="slider slider-{{ app.color }} round"></span>
            </label>
        </div>
        <br>
        <p class="text-right">App Contributors:</p>
        <p class="text-right">{{ app.contributors }}</p>
    </div>
</div>
{% endfor %}
```
Then, in the site's profile page, it was as simple as calling the tag's function, which loaded the previously defined template into the page.
```
<div class="flex-container icon-row">
    {% display_apps request %}
</div>
```
In addition, I made the preference buttons dynamically functional as well. Each button was basically a checkbox input tag, and was given an id value based on the app name. Upon being checked or unchecked, a jquery script triggered an AJAX post request, containing the button ID and bool value.
```
$('.pref-button').click(function(){
    var id = $(this).attr("id");
    id = "#" + id
    change_preferences(id);
});
.......
function change_preferences(id) {
    
    //Check if slider is on or off
    var result;
    if($(id).is(':checked')) result = "True"; else result = "False";
    
    var csrftoken = getCookie('csrftoken');

    var dict = {
        button: id,
        value: result
    };

    //Make ajax post with the result of the button
    $.ajax({
        url: 'save_preferences/', //Target python function is called "create_post"
        type: "POST",
        data:  {csrfmiddlewaretoken: csrftoken, text: dict}, //Result of button
        error : function() { //Error message
            alert("Something went wrong with that AJAX call.");
        },
    });
};
```
When the server received the AJAX post reuqest, it saved the value to the appropriate entity in the database.

4) Making the homepage show only active apps. One of the user preferences for each app controlled whether this app appeared on the homepage dashboard of apps, for easy access. Just as I did for the profile page, I created a custom tag that would load only those app icons that the user has decided to display.  On the server side for this tag, I queried each app preference table for that user's preference. If the answer was "yes" to display this app, the function would add the details for this app to the list of apps.
```
#This function displays only those apps that the user has decided to show
  @register.inclusion_tag('CustomTags/homepage_apps.html')
  def show_active_apps(request):
  #This can be deleted after Development. This creates preference tables for those whose accounts were made before this was implemented
    #===================
    if not BandsPreferences.objects.filter(user_id=request.user.userprofile.user_id):
        create_prefs(request.user)
    #===================
    active_apps = []
    all = AppProps.objects.all()  #Get list of all apps and their details
    for prop, app in zip(all, all_apps):
        name = prop.app_name
        #Call function to get details for current app
        currentApp = get_details_HmPg(name, app, request.user.id)
        if currentApp: #Add to list of active apps if the function returns an app
            active_apps.append(currentApp)

    return {'apps': active_apps}

  #Returns details for an app if user has selected this app to be active        
  def get_details_HmPg(name, app, cur_user):
      preference = app.objects.get(user=cur_user)#Find current user's preferences for this app
      if preference.offOn == True: #If this app is to be displayed:
          row = AppProps.objects.get(app_name=name) #Find details for this app
          title = row.title
          icon = row.icon
          url = row.url
          cur_app = HomeApps(title, icon, url)
          return cur_app
```
This list was then sent to the HTML template, where it was unpacked creating as many app icons on the page as there were active apps.
```
{% for app in apps %}
<div class="col-lg-3 col-md-3 col-xs-3 thumb mb-5">
    <a href='{{ app.url }}'><i class="fas {{ app.icon }} fa-5x"></i><br>{{ app.title }}</a>
</div>
{% endfor %}
```
