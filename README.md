# Tech-Academy-Projects
Learning projects while taking courses at Tech Academy

## Python Live Project with Django Framework

I participated in a two-week Python Live Project while attending The Tech Academy. I was tasked with creating a web application using the Django Framework. The purpose of the application was to allow functionality to create, review, update, and delete information from a database. Numerous functions in the views were used to retrieve information and display them on the applicable HTML template. Here are some highlights from the project:

### Creating the base model:
My page was centered on keeping records of popular books, so I used the following model to establish what attributes the records would have. Using the models.Manager() allowed for the records to be listed by the title of the book when displaying all records, which looked much cleaner than the default setting for new records. 
```
class Book(models.Model):
   Title = models.CharField(max_length=70, default='')
   Author = models.CharField(max_length=50, default='')
   Series = models.CharField(max_length=50, default='')
   Synopsis = models.TextField(max_length=750, default='')
   Review = models.TextField(max_length=750, default='')
   Rating = models.DecimalField(max_digits=2, decimal_places=1, default=0.0)

   objects = models.Manager()

   def __str__(self):
       return self.Title
```

### Creating a new database entry: 
The following function served to allow users to create a new database entry when the “add new entry” button was selected from the index page. I used the built-in Django modal form to establish what information was to be input and added checks to ensure that all information was entered prior to saving the new record to the database. The modal form was based on the previously established model called “Book”.
```
# allows new record to be created and saved to dB
def novelEntry(request):
   form = NovelForm(data=request.POST or None)
   if request.method == 'POST':
       if form.is_valid():
           form.save()
           return redirect('Novels_create')
   context = {'form': form, }
   return render(request, 'Novels/Novels_create.html', context)
```


### Displaying all database records:
The following function served as a way to display all current database records on the HTML template. All records were saved as a variable and then passed into the applicable template. This was an overview of all records but only included the title of the book and the author’s name so as to not clutter the page. 
```
# displays all records
def novelDisplay(request):
   all_novels = Book.objects.all()
   return render(request, 'Novels/Novels_display.html', {'all_novels': all_novels})
```

### Specific Database Information Requests and Database Edits:
A function was created to display the full record of any specific record depending on user request from a drop-down menu of all records. The specific record was passed into the applicable template for viewing. This function also allowed for users to make edits to an existing record of their selection and would prompt for confirmation prior to confirming edits. Once edits are confirmed the new data overwrites the existing data in the database. 
```
# function allows all data for one record to be viewed and edited
def novelDetails(request, pk):
   pk = int(pk)
   book = get_object_or_404(Book, pk=pk)
   form = NovelForm(data=request.POST or None, instance=book)
   if request.method == 'POST':
       if form.is_valid():
           form2 = form.save(commit=False)
           form2.save()
           # want to add an alert that says changes were made. This does not currently work?
           messages.info(request, 'Changes made successfully.')
           return redirect('Novels_display')
       else:
           print(form.errors)
   else:
       return render(request, 'Novels/Novels_details.html', {'form': form})
```
### Delete Function:
The last of the primary views function for this application was a delete function. Since the models are all assigned a primary key at time of creation, this primary key was used to identify which record had been selected. When the delete button is pressed it redirects the user to a page requesting confirmation prior to deleting the record from the database. 
```
def novelDelete(request, pk):
   pk = int(pk)
   book = get_object_or_404(Book, pk=pk)
   if request.method == 'POST':
       book.delete()
       return redirect('Novels_display')
   context = {'book': book}
   return render(request, 'Novels/Novels_delete.html', context)
```

### API Connection and Response:
The application utilizes an API that retrieves information regarding definitions, synonyms, etc of a particular word. A function was created to allow the user to input a specific word and the API would return a response. Using a helper function allowed for the API JSON response to be parsed, returning only the definition(s) for the given word. 
```
# user can input a word and all definitions from API are returned
# assisted by helper function parseDefine() to iterate through dictionaries in API
def defineWord(request):
   context = {}
   if 'word' in request.GET:
       word = request.GET['word']
       url = 'https://api.dictionaryapi.dev/api/v3/entries/en_US/%s' % word
       response = requests.get(url)
       define = response.json()
       definition_list = parseDefine(define)
       print(definition_list)
       context = {'define': define, 'definition_list': definition_list}
   return render(request, 'Novels/Novels_define.html', context)
(Here is the helper function. Variable from the main function is passed into the helper function then back into the main function to complete the request.)
# parse JSON response from defineWord() - JSON response stored in variable 'define'
# helper for defineWord()
def parseDefine(define):
   all_definitions = []
   definitions = define[0]['meaning']
   for word_type in definitions:
       for word_def in definitions[word_type]:
           print(word_def['definition'])
           # makes response as 'word type': 'definition' for each iteration
           all_definitions.append(f"{word_type}: {word_def['definition']}")
   return all_definitions
```

### Beautiful Soup Integration:
Using the Beautiful Soup Python module, I created a function that parsed through HTML elements of a selected webpage, returning only the desired paragraph elements. This was aided by a helper function that was utilized to parse through the HTML response, returning only the paragraph elements from a specific section of the requested URL. 
```
# Use Beautiful Soup to retrieve information from external web page
def soupRead(request):
   page = requests.get('https://www.healthline.com/health/benefits-of-reading-books')
   soup = BeautifulSoup(page.content, 'html.parser')
   body = soup.article
   para_text = parseSoup(body)
   context = {'para_text': para_text}
   return render(request, 'Novels/Novels_why.html', context)
```


(Below is the helper function. Variable from the main function was passed into the helper function and then back to the original function to complete the request.)


```
# parse through HTML for paragraph elements
# helper for soupRead()
def parseSoup(body):
   content = []
   for paragraph in body.find_all('p'):
       print(paragraph.text)
       content.append(paragraph.text)
   return content
```


### Conclusion:
The above code snippets highlight some of the major functionalities of the app I created, but there was much more that went into this project. I also utilized HTML to create templates, CSS for styling, and also worked with Django commands to extend the HTML templates and link the appropriate urls on the switchboard. 

I learned many things over the course of this project, including but not limited to:

* Working with a team of developers
* Utilizing resources to achieve desired results (online research, consulting with peers, asking for direction from project managers/instructors)
* Importance of open and honest communication with team regarding successes and struggles
* Django Framework concepts
* API’s and JSON response 
* Debugging and troubleshooting in Python
* Chrome DevTools
* DevOps 



========================================================================================================================================================================================



## C# Live Project with .NET Framework

I participated in a two-week Live Project using the .NET Framework to create a Content Management Service app as part of my studies with the Tech Academy. I worked with a team of developers to create a web application for a Theatre Production Company based in Portland, Oregon. The goal of the project was to create an application that the theatre staff could use to display information about various aspects of their business, from rentals to blogs to upcoming productions. It was designed in such a way that the staff would be able to create, edit, delete and update information themselves without needing to implement any code. The changes could be made directly from the web application on their end through the Content Management Service (CMS). The application was created as an MVC application.

My tasks during the project revolved around the “Productions” area of the site. Here are some highlights from my contributions to the project: 


### Creating the Productions model:
This project utilized code-first programming, so my first task was to create the “Productions” class that would contain all the relevant properties for the theatres’ upcoming productions. Once the class was created I applied migrations, updated the database, and added scaffolding to create the related views for the new class. The class properties were chosen to fit the needs of the theatre business in order to keep track of important aspects of their productions.

```
public class Productions
    {
        [Key]
        public int ProductionId { get; set; }
        public string Title { get; set; }
        public string Description { get; set; }
        public string Playwright { get; set; }
        public int Runtime { get; set; }
        public DateTime OpeningDay { get; set; }
        public DateTime ClosingDay { get; set; }
        public DateTime ShowTimeEve { get; set; }
        public DateTime? ShowTimeMat { get; set; }
        public int Season { get; set; }
        public bool IsWorldPremiere { get; set; }
        public string TicketLink { get; set; }
        public bool IsCurrentlyShowing { get; set; }
    }
}
```



### Styling CRUD Pages:
Next I added formatting and styling to the newly created pages related to CRUD functionality for the Productions. The theatre company had a particular color palette selected and these colors were defined as variables in the shared CSS project file. I used a combination of bootstrap and CSS to make the new CRUD pages styled appropriately so that the colors for the buttons, backgrounds, and input forms matched the rest of the application. 

### Index Page Cards/buttons/modals:
Using bootstrap I changed the index page so that the records in the database display as cards with an image/title at the top and the description at the bottom of each card. I used a “foreach” loop to iterate through all the database records and display each one on the page as seen in the below snippet. (Note that the image source is a placeholder, another developer is tasked with creating the related class for the Production photos and linking the respective id to the main Production class). 

```
<!--Card container-->
<div class="container">
  <div class="card-columns">
    @foreach (var item in Model)
    {
      <!-- Display Cards -->
    <div class="card">
      <img src="http://www.fillmurray.com/g/200/300" alt="placeholder" class="card-img-top img-fluid" />
      <div class="card-img-overlay">
        @Html.ActionLink("Edit", "Edit", new { id = item.ProductionId }, new { @class = "badge badge-pill" })
        @Html.ActionLink("Delete", "Delete", new { id = item.ProductionId }, new { @class = "badge badge-pill" })
      </div>
      <div class="card-body" style="transform: rotate(0);">
        <h3 class="card-title"> @Html.DisplayFor(modelItem => item.Title)</h3>
        <p class="card-text">@Html.DisplayFor(modelItem => item.Description)</p>
        <a href="javascript:void(0);" class="anchorDetail stretched-link" data-id="@item.ProductionId"></a>
      </div>
    </div>
 <!-- End Display Cards -->
 <!-- Modal -->
    <div id="myModal" class="modal">
      <div class="modal-dialog">
        <div class="modal-content">
          <div id="myModalContent"></div>
        </div>
      </div>
    </div>
 <!-- End Modal -->
    }
  </div>
</div>
<!-- End Card Container-->
```

I added buttons to each card that appear when the card is hovered. These buttons link to edit and delete pages for each record that allow the user to make edits to an existing record and save the changes, or delete the record entirely. 

I also created a modal that gets triggered when the description at the bottom of each card is clicked. The content of the modal is dynamically populated based on which card is clicked. I added a function in the controller that would render a partial view in the modal for the respective card as seen below:

```
//Link to partial view for Index page details modal
        public ActionResult IndexModal(int ProductionId)
        {
            Productions productions = db.Productions.Find(ProductionId);
            return PartialView(@"~\Areas\Prod\Views\Productions\_Details.cshtml", productions);
        }
```

I created a script using jQuery and ajax that would be triggered when the production description on the card was clicked. This would call the controller method and pass in the id and open the modal where the partial view displays. 

```
//Modal script 
var ProductionDetailsURL = '/Productions/IndexModal';
$(function () {
    $(".anchorDetail").click(function () {
        var $buttonClicked = $(this);
        var id = $buttonClicked.attr('data-id')
        var options = { "backdrop": "static", keyboard: true };
        $.ajax({
            type: "GET",
            url: ProductionDetailsURL,
            contentType: "application/json; charset=utf-8",
            data: { "ProductionId": id },
            datatype: "json",
            success: function (data) {
                $('#detailsModalContent').html(data);
                $('#detailsModal').modal(options);
                $('#detailsModal').modal('show');
            },
            error: function () {
                alert("Something went wrong. Failed to load.");
            }
        });
    });
    $("#closebtn").click(function () {
        $('#detailsModal').modal('hide');
    });
});
```

### Conclusion:
These were just some of the major components of the application that I created. I utilized a combination of HTML, Razor, CSS, bootstrap, C#, and JavaScript to build this app.  

I learned a lot while working on this application, some of the more notable things being:

* Working with a team of developers
* Utilizing resources to achieve desired results (online research, consulting with peers, asking for direction from project managers/instructors)
* Importance of open and honest communication with team regarding successes and struggles
* .NET Framework
* JavaScript/jQuery
* bootstrap 
* Debugging and troubleshooting in C#
* Chrome DevTools
* DevOps (Azure)


