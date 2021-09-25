# Tech-Academy-Projects
Learning projects while taking courses at Tech Academy

## Python Live Project with Django Framework

I participated in a two-week Python Live Project while attending The Tech Academy. I was tasked with creating a web application using the Django Framework. The purpose of the application was to allow functionality to create, review, update, and delete information from a database. Numerous functions in the views were used to retrieve information and display them on the applicable HTML template. Here are some highlights from the project:

##### Creating the base model:
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

##### Creating a new database entry: 
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


##### Displaying all database records:
The following function served as a way to display all current database records on the HTML template. All records were saved as a variable and then passed into the applicable template. This was an overview of all records but only included the title of the book and the author’s name so as to not clutter the page. 
```
# displays all records
def novelDisplay(request):
   all_novels = Book.objects.all()
   return render(request, 'Novels/Novels_display.html', {'all_novels': all_novels})
```

##### Specific Database Information Requests and Database Edits:
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
##### Delete Function:
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

##### API Connection and Response:
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

##### Beautiful Soup Integration:
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


##### Conclusion:
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

