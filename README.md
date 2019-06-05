# LiveProjects
Web Development Projects

Over the last three months at the Tech Academy, I worked on a team of fellow junior developers as a fullstack website developer.  With a total of 5 different two-week sprints, I gained a lot of confidence in working in both Django and ASP.NET frameworks.
In the first sprints, I helped create and improve various features of a django website that fetched and aggregated data based on user-preferences, such as weather alerts, current traffic, sport events, concerts, movies, and podcasts.
In the second sprint, I worked on an admin site for a Erectors, Inc. The purpose of this site was to allow project managers to assign and manage tasks adn task details for each of the employees, and allow everyone to chat with each other about related questions.
In the last 3 sprints, I worked on a revamped version of the previous Django Datascraper, one sprint on Frontend, Backend, and Fullstack each.


## First Sprint (Python full stack):
1. Home Page. I added to the homepage a grid of photos representing the different features of the website, which when clicked, directed the user to the latest updates regarding that category, and displayed a error-modal if access is attempted to a page without the user being logged in.
2. Populating a dropdown list for sending messages to friends.
3. Weather Alerts. Queried the weather API for current weather alerts, including title, times, and areas affected. Parsed the JSON string and saved appropriate parts to variables in a dictionary, and passing this dictionary to the HTMl I displayed the information nicely formatted. I ran into a bug around the method "zip" -- someone had previously defined a variable of this name and 
4. Add Movie Images. Retreived the URLs for the top movie titles from an online API via JSON, and displayed them tabularly in a view.
6. Events. Query event API for relevant data by zipcode, then if returns nothing, query by cityname.  Send this dictionary of variables to HTML where it was displayed appropriately.
Change preferences. Auto-populate list of favorite sports teams, and retrieve the user's favorite team.

## Second sprint (C# fullstack):
1. Redisigning the Navbar. I looked at the existing sister site as the model for the styling. Using jquery to alter the opacity of the navbar upon scrolling.
2. Creating a chatbox (front end). Learned about textarea tag in HTML, how to create a partial view layered on an existing view and common/visible on specific pages, the importance of referencing JS/Jquery tags in the right order. I styled the box similarly to the navbar, and made it collapsible.  The heading had the name of the person to whom the message was being sent. Then I added a dropdown list from which the user could choose who to send the message to.  Since the chatbox was supposed to be accesible on different pages, I could only pass a model to it from a controller that was common to the various controllers for the different pages. So I created a base controller from which the others inherited, and in that controller I queried the database for all the site users who would potentially become recipients for the messages.
3. Saving the messages to the DB.  On the server side chathub class, I was able to retreive the necessary data and save to the DB.
4. Creating a code-first model for Entity Framework with foreign keys. (no code snippet)
5. Making a controller to get list of schedules from DB and organizing them according to jobs, then creating a view page where this data can be displayed in a tabular form.


## Third sprint (Python Front-end only):

1. Readded missing login button.
2. I was responsible for making the entire website have a uniform styling in terms of fonts, colors, and spacings. I made plenty use of bootstrap features, specifically rows and columns to organize the different elements in. I was given a palette of theme colors to choose from, and apply them to buttons, backgrounds and text. To do so, I created several CSS variables for these colors that could be easily accessed anywhere in the CSS. The different HTML elements made use of these variables, so if ever a slight tweak of the colors were necessary, they could all be changed at one stroke.
3. Added an app manager to the profile page. Basically a bunch of compartments, each containing the app icon, several slider buttons, description, and list of app contributors. The icons and buttons are colored according to the agreed on colors for the apps.
4. The news-app was just a static page with the headline and photo for each news article.  I converted this into a carousel in which each entity was the news photo with an overlay of the headlines, and the user could go through each one by clicking forward and backward arrows. Then i added a slider by which, though not fully functional, allowed the the user to navigate through the different headings.
5. Added a default profile image if the user has not selected a personal one, and eliminated an upload button by automatically uploading/changing the picture once the user has selected a profile pic from a file explorer.

## Fourth Sprint (Python Back-end only):

1. Account App. Goal: allow user to switch a button allowing him to toggle automatic geolocation or not. Added slider to HTML, linked it to AJAX call which would post when the slider was switched from its current position. (In this process, discovered the necessity of installing a function that would create a csrf token) The setting was saved to database. The weather app (the only one at the moment using geolocation) was updated so that it would check the database whether to fetch current location first before finding weather, or use the default location stored in the DB.
2. Traffic App. Goal: display directions on map. At first I tried getting images of the map by accessing Mapquest's main API, which returns a JSON. However, all the images returned were too small and none showed the directions from start to finish, but only sections. Then I switched to Mapquest's JS API, where I could load real zoomable maps onto the HTML.  I queried the API's portals by giving them the starting and ending locations that the user entered into the text boxes.  I set up the page so that it would only show the map, and display the directions on the page, after the user had already requested directions from the main API.
3. Messaging App. Goal: add response functionality to message app. To do this, I added a button to each message in the inbox, that when clicked on, would display a textbox where the user could respond to the message, as well as a button to send the message.  On the backend, I created another column in the database to keep track of message threads, so that the message couuld eventually be displayed nicely on the page grouped according to the conversation thread (I did not actually implement this part).  The value of the conversation thread was the message of the id of the first message that was responded to.
4. Movie App. Create an app that will allow the user to view most popular movies, sort them by year and/or genre, and change the number to display on the page. For each movie, there is a toggle button to allow the user to view details on the movie, such as year, genre, director, and plot. For setup I added a movie class with the properties already mentioned, and added a model/DB table for saving the user preferences for display and sort options, set up the HTML page with appropriate buttons and drop down list. The view consisted of two classes, one to deal with the the get/post requests and another to actually fetch the list of movies. The first class checked the user display preferences, saved any new changes to the DB, and passed these changes to the second class so it could fetch movies accordingly. Using beautiful soup and parsing techniques, I got the top 100 movie title from IMDB. Then for each movie, using the title as parameter, I queried the OMDB API for details for each movie.  This involved installing python's w3lib module to make the title string into a safe parameter with only ASCII characters, to remove the possibility of foreign characters being passed in. The OMDB API returned a JSON object which I parsed for the data I wanted, then saved the appropriate data to the appropriate properties of a movie class instance.  A list of movies was thereby created, and returned in a context to the HTML.
In order to implement the genre search efficiently, before creating a movie instance and adding it to the list, I checked the JSON response to see if the movie included the genre the user requested, and if not, it simply returned to the top of the loop to find the next movie.
To implement the year sort, instead of sorting the list of movies by genre after the list was created, I wrote a simple algorithm to insert the new movie at the correct place in the list each time a new movie was fetched, so that by the end the list was alreadt sorted.
To implement the display feature, the loop to fetch movies was ran until the length of the movie list was as long as the desired display.

## Fifth Sprint (Python full stack):
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
