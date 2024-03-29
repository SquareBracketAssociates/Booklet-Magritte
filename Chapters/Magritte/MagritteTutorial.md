## Magritte KatasThis chapter presents some exercises about Magritte. They are based on the Magritte Tutorial written by Lukas Renggli. We thank him for Magritte and this set of exercises. The exercises has been ported to Magritte 30 and Seaside 3.1. Magritte30 supports instance-based declarations while Magritte20 was only supporting class-based declarations. The exercises have been enhanced and are maintained by Stéphane Ducasse and Damien Cassou. The exercises start simple and with detailed instructions on how to perform the given tasks. Exercises marked with a star are a bit trickier, you might want to solve them later on. Sometimes you will probably not exactly know what class to use, what method to call or what parameters to pass, use the power of Pharo \(senders, implementers, references, ...\) to browse the source-code of Magritte, you might even discover some other features that were not presented during this tutorial.### Getting Started Get an image and bring the Catalog Browser and install the stable version of MagritteContactManager.In case you need to know where the code of the tutorial is saved. It is in the following repository:```language=smalltalkMCSmalltalkhubRepository
	owner: 'Magritte'
	project: 'Magritte3'
	user: ''
	password: ''```Make sure your Seaside server is running within the image by browsing the counter application at [http://-local-host:8080/examples/-counter](http://-local-host:8080/examples/-counter)If it is not, the seaside control panel from Tools menu of the world menu. ### Juggle with DescriptionsHave a look at the source-code of the classes `CMPerson`, `CMAddress`, and `CMPhone`. In essence a person is described by different attributes such as it first name, name email, birthday... In addition it is in relation with an address object and it has a list of phones. Here are the class definitions. Note that the class names are a bit overkill with the Model prefix. In future versions, we may drop it. ```Object subclass: #CMPerson
    instanceVariableNames: 'title firstName lastName homeAddress officeAddress picture birthday phones password'
    classVariableNames: ''
    package: 'Magritte-ContactManager'``````Object subclass: #CMPhone
    instanceVariableNames: 'kind number'
    classVariableNames: ''
    package: 'Magritte-ContactManager'``````Object subclass: #CMAddress
    instanceVariableNames: 'street plz place canton nationality'
    classVariableNames: ''
    package: 'Magritte-ContactManager'```All the following exercises will be built upon this simple model.    Browse to [http://localhost:8080/personeditor](http://localhost:8080/personeditor) and check if you can see all the features presented during the lecture. You should get an application as shown in Figure *@editor@*.![A person editor.](figures/personeditor.png label=editor&width=60)   ### Exercise 1: Adding a simple field Add a new field that holds a _Comment_ about the person, display it as a text-area field as the last element. Test it in the Web browser by starting a new session on the same application. If you are required to add more than one new method by hand you did something wrong.   #### Solution.Add the following instance variable and accessor. ```language=smalltalkCMPerson >> comment
    ^ comment ```In the `MADescription` hierarchy, there is a class `MAStringDescription` which is used to describe a string. You can try to use it like this:```language=smalltalkCMPerson >> descriptionComment
    <magritteDescription>
    ^ MAStringDescription new
            accessor: #comment; 
            label: 'Comment';
            priority: 100 ; 
            yourself``` The form input rendered is a text-input whereas the subject asks you for a text-area \(a text-input on more than one line\). Have a look at `MAStringDescription` subclasses. You will see a `MAMemoDescription` class with a `lineCount:` method. Use this class as follows:```language=smalltalkCMPerson >> descriptionComment
    <magritteDescription>
     ^ MAMemoDescription new
                 accessor: #comment; 
                 label: 'Comment';
                 priority: 100 ; 
                 yourself```A text-area is then displayed.### Exercise 2: Adding Nationality as a single selectionAdd a new field that holds the nationality of the person, display it as a sorted drop-down box with a few selectable countries. Put it right after the address field. The default choice for new objects should be Switzerland.#### Solution.When you want to ask the user to choose one element in a list of multiple, you should use the `MASingleOptionDescription` class. This class is a subclass of `MAOptionDescription` which accepts the `options:` message to specify the different choices. The default choice \(displayed by Magritte if no other choices have been selected\) is chosen with the `default:` message:```language=smalltalkCMPerson >> descriptionNationality 
    <magritteDescription>
     ^ (MASingleOptionDescription new 
            accessor: #nationality;
            label: 'Nationality';
            priority: 55; 
            yourself)
            options: #( 'Switzerland' 'France' 'Germany' ); 
            default: 'Switzerland';
            beSorted; 
            beRequired;
            yourself``` You can use the `beSorted` message to sort values in the list.You can add also the `beRequired` to make sure that the user cannot choose an empty item in the list. ### Exercise 3: Adding emailAdd a new field that holds the E-Mail of the person, display it as a required text-field. Add custom conditions to force the user to give a valid e-mail address. Don't allow addresses from the providers hotmail.com and gmx.com, gmx.de, gmx.it, etc. Test your code in the Web browser.#### Solution.An E-Mail is basically a string. So, to start, you can just ask for a string:```language=smalltalkCMPerson >> descriptionEmail
    <magritteDescription>
     ^ MAStringDescription new
            accessor: #email;
            label: 'Email';
            priority: 95;
            yourself```If you open a browser on `MADescription` class \(protocol validation\), you will see that we can send the message `addCondition:labelled:` to a description```language=smalltalkCMPerson >> descriptionEmail
    <magritteDescription>
    ^ (MAStringDescription new
        accessor: #email;
        label: 'Email';
        priority: 95;
        yourself)
        addCondition: [ :value | value matches: '#*@#*.#*' ] labelled: 'Please enter a valid email';
        addCondition: [ :value | (value matches: '#*@hotmail.com') not ] labelled: 'Hotmail users not allowed';
        addCondition: [ :value | (value matches: '#*@gmx.#*') not ] labelled: 'GMX users not allowed';
        beRequired; yourself```### Exercice 4: Reusing DescriptionsYou can also reuse a description. For example, we will reuse nationality description from `CMPerson` in the address `CMAddress` by calling the appropriate description of the person  and changing the label to `Country`. Make sure not to modify the original description by creating a copy. Test it in the Web browser.#### Solution. Descriptions are created in methods. To reuse a description, you can just send the method:```language=smalltalkCMAddress >> descriptionCountry
    <magritteDescription>
    ^ CMPerson new descriptionNationality copy
        label: 'Country'; 
        yourself```![The Nationality description is reused by the `CMAddress`.](figures/addressWithCountry.png)Note that here we create a new person because there is no reference from `CMAddress` to `CMPerson`.### Exercise 5: Food for Thought Before starting to use Seaside in combination with Magritte it is worth to step back and ask ourself some questions:- Make a rough guess on how much code was saved by using Magritte compared to a manual approach in Seaside. - From when on does it make sense to use a meta-model? - What are the advantages / disadvantages?## Seaside integration katasUp to now we have been working with descriptions only, we didn’t write any line of Seaside code. Moreover the model was not remembered somewhere and therefore was lost between different sessions. In this section we will concentrate on Seaside and build a simple user-interface that allows us to manage multiple persons. To get an idea of how the result could look like, see Figure 1.### Exercise 6: Introducing the Person Manager ApplicationStart by creating a new subclass of `WAComponent` called `MAPersonManager`. Add a class instance-variable called `Persons` that will serve us as a simple place to keep the model objects. Initialize it with an empty `OrderedCollection`. Register the newly created class as a new Seaside entry point named `personmanager`. Create a method `renderContentOn:` that displays the heading 'Person Manager'.Test the setup of your new application in the Web browser.#### Solution.```language=smalltalkWAComponent subclass: #MAPersonManager
    instanceVariableNames: ''
    classVariableNames: 'Persons'
    package: 'Magritte-ContactManager'``````language=smalltalkMAPersonManager class >> initialize
    "self initialize"
    
    super initialize.
    self persons: OrderedCollection new.
    WAAdmin register: self asApplicationAt: 'personmanager'``````language=smalltalkMAPersonManager >> renderContentOn: html
    html heading: 'Person Manager'. ```### Exercise 7: Adding a contactIn the `MAPersonManager` class, define the method `add`, that creates a new instance of `CMPerson`, calls the default Magritte editor and adds it to our collection. Note that Magritte will answer `nil`, when the user hits the cancel button in the editor. Create a link in your page that will execute the `add` method.#### Solution. A Seaside component is created from a model using the message `asComponent`. Around a component, you can add buttons to save and cancel using the message `addValidatedForm`. If you want to use another component instead of the current one, use the message `call:`. This method returns what answered the component:```language=smalltalkMAPersonManager >> add
    | person |
    person := self call: (CMPerson new asComponent addValidatedForm; yourself).
    person isNil
        ifFalse: [ self class persons add: person ]```Let's see how one add a link to a page:```language=smalltalkMAPersonManager >> renderContentOn: html 
    html heading: 'Person Manager'.
    html anchor
        callback: [ self add ]; with: [ html text: 'add']```### Exercise 8: Rendering the contactsCreate an accessor `persons` for the class-variable Persons on the instance-side of `MAPersonManager`Render a list of all persons within MAPersonManager. #### Solution 8.We define a simple accessor either using the class side message or directly accessing the variable. ```language=smalltalkMAPersonManager >> persons 
    ^ self class persons```or ```language=smalltalkMAPersonManager >> persons 
    ^ Persons```We modify the rendering method to display the persons. ```language=smalltalkMAPersonManager >> renderContentOn: html 
    html heading: 'Person Manager'. 
    html anchor
        callback: [ self add ];
        with: [ html text: 'add' ]. 
    html break.
    self persons
        do: [ :person | self renderLineForPerson: person on: html ] separatedBy: [ html break ]```For this we introduce a dedicated method: ```language=smalltalkMAPersonManager >> renderLineForPerson: aPerson on: html
    html 
        text: aPerson firstName; 
        space; 
        text: aPerson lastName```### Exercise 9: Enhancing the listImprove the list entries rendering so that existing persons can be edited. #### Solution 9.First we define a new method named `editPerson:` which presents its argument as a form component. ```language=smalltalkMAPersonManager >> editPerson: aPerson
    self call: (aPerson asComponent addValidatedForm; yourself)```Second we modify the `renderLineForPerson:on:` method to add a link that invokes the previous method. ```language=smalltalkMAPersonManager>>renderLineForPerson: aPerson on: html 
    html anchor
        callback: [ self editPerson: aPerson ];
        with: [ html text: aPerson firstName; 
                    space; 
                    text: aPerson lastName ]```![Persons with edit and view.](figures/personmanager-list2withView.png)### Exercise 10: Readonly Render an additional link called view, as seen in Figure 1, to display the entry in a read-only view. You can turn a component into read-only mode by sending the message `readonly:` with a boolean stating the read-only status.#### Solution.First we define a method that will show the values of a component. ```language=smalltalkMAPersonManager >> viewPerson: aPerson 
    self call: (aPerson asComponent 
        addForm: #(cancel); 
        readonly: true; 
        yourself)``````language=smalltalkMAPersonManager >> renderLineForPerson: aPerson on: html 
    html anchor
        callback: [ self editPerson: aPerson ];
        with: [ html text: aPerson firstName; 
                        space; 
                        text: aPerson lastName ].
    html space.
    html anchor
        callback: [ self viewPerson: aPerson ];
        with: [ html text: 'view' ].```## Using Magritte ReportsRendering a list like this is nice, but it doesn’t scale well if you have many entries. As well it doesn’t take advantage of the nice descriptive model we have. Luckily Magritte includes built in reporting facilities that we will be extending our application with. For example professional applications such as the one of Quuve are fully developed with Magritte and based on Magritte report.  In this section we make our little application look like Figure 2.### Exercise 11: A first report Add an instance variable that will refer to the report. Add accessors. Add a new child component to `MAPersonManager` and initialize it, lazily or within the method `initialize`, with the following expression `MAReport rows: self persons description: self persons magritteDescription`. Pay attention that the `children` message should return all the subcomponent of the receiver. Once done, render the report instead of the list.#### Solution 11. ```language=smalltalkWAComponent subclass: #MAPersonManager
    instanceVariableNames: 'report'
    classVariableNames: 'Persons'
        category: 'Magritte-ContactManager'``````language=smalltalkMAPersonManager >> report 
    ^ report
MAPersonManager >> report: aReport 
    report := aReport    ```We initialize the report following the ```language=smalltalkMAPersonManager >> initialize
    super initialize.
    self report: (MAReport rows: self persons description: CMPerson new magritteDescription)```Now we define the method `children` to make sure that the report component is correctly returned. ```language=smalltalkMAPersonManager >> children
    ^ OrderedCollection with: self report``````language=smalltalkMAPersonManager >> renderContentOn: html 
    html heading: 'Person Manager'. 
    html anchor on: #add of: self. 
    html render: self report```### Exercise 12: Handling adding refreshPlay with the report in your browser: sort the rows, add new persons... You will probably notice that the report doesn’t update itself when adding new persons.  It's because the report creates a copy. Call the message `refresh` on the report to fix this problem. #### Solution 12We change the definition of the `add` method. ```language=smalltalkMAPersonManager >> add 
    | person |
    person := self call: (CMPerson new asComponent addValidatedForm; yourself).
    person isNil 
        ifFalse: [ 
            self persons add: person.
            self report refresh ]```### Exercise 13: Filtering informationRight now almost all the information about a person is displayed within the report, if you enter much data this gives a very wide Web page. Filter out all the descriptions except the ones for `firstName`, `lastName` and `email`.#### Solution 13. ```language=smalltalkMAPersonManager >> filteredDescriptionsFrom: aPerson

    ^ aPerson magritteDescription select: [ :each | 
         #(firstName lastName) includes: each accessor selector ] ```     ```language=smalltalkMAPersonManager >> initialize
    super initialize.
    self report: (MAReport rows: self persons description: (self  filteredDescriptionsFrom: CMPerson new))```### Exercise 14: Adding extra columnAdd a new column to the report to hold commands to view and edit entries. To get a hint on how to achieve the desired behaviour, browse the references of `MACommandColumn`.#### Solution 14.We define an instance of `MACommandColumn` to group two commands. Note that we use two variants to show that we can control the text displayed from the action performed. ```language=smalltalkMAPersonManager >> initialize
	super initialize.
	self report: (MAReport rows: self persons description: (self  filteredDescriptionsFrom: self persons first)).
	self report addColumn: (MACommandColumn new 
						addCommandOn: self selector: #viewPerson:; 
						addCommandOn: self selector: #editPerson: text: 'Edit'; yourself)```### Exercise 15: Adding a search facilities We will now implement a search functionality within your report. The user should be asked for a filter string that will be compared to all the string-fields within the persons. To get some pointers have a look at the implementors and senders of `rowFilter:`, `toString:` and `readUsing:`, and `matches:`. Add a clear link as well, to remove any filter.#### Solution 15.The method `rowFilter:` expects a block returning a boolean telling if the row should be shown. ```language=smalltalkMAPersonManager >> filter
	| filter |
	filter := self request: 'Enter a filter string'. 
	self report rowFilter: [ :person |
		(self report columns collect: [ :col |
			col magritteDescription in: [ :desc | desc toString: (person readUsing: desc) ] ]) anySatisfy: [:value | value matches: filter ] ]```The solution does not compare the search to only string fields but to all the values of the receiver converted to strings.The following expression ```(self report columns collect: [ :col |
	col description in: [ :desc | desc toString: (person readUsing: desc) ] ])```returns a collection of strings of the value held by the receiver \(here a person\). Clearing the filter is simply to set its argument to `nil`. ```language=smalltalkMAPersonManager >> clear
	self report rowFilter: nil``````language=smalltalkMAPersonManager >> renderContentOn: html
	html heading: 'Person Manager'.
	#( add filter clear )
		do: [ :symbol | html anchor on: symbol of: self ]
		separatedBy: [ html space ]. 
	html render: self report```## Giving access to end-usersAs experience shows, customers are often not completely satisfied with the features the application developers are giving to them. They want more and they want to be able to customize everything by themselves. Thanks to the reflective nature of Magritte - all the descriptions within Magritte are described with Magritte descriptions itself - we can easily provide the necessary functionality to offer flexibility to end-users. ### Question 16 Point your browser to http://localhost:8080/magritte/editor. You should get a browse navigating the different descriptions themselves. What you see is that a description is an object having multiple fields. In addition since a description is also described using magritte we can get easily a editor to edit descriptions. Browse the class `MADescriptionEditor`: it is the one implementing the description example editor itself. The class `MADescriptionEditor` uses an instance of `MAAdaptativeModel`. This class is just a mock to act as a described object.It has only two instance-variables: one references a description-container, the other a dictionary mapping description-elements to actual values. Have a look at the methods `readUsing:` and `write:using:`, and their default implementations in Object. Why are those methods overridden in this class? Because this class can represent any object but the object field values are not stored the same way as a normal object.### Question 17: not sure that we will keep it.Add a class variable to `CMPerson` called `CustomDescription`. Initialize it with an empty instance of `MAContainer`. Create an accessor method. Override `magritteDescription` on the instance-side, call super and compose it with the `CustomDescription`. So far, you should see no difference in the behaviour of the application when testing it.#### Solution 17.```language=smalltalkCMPerson class >> customDescription 
	^ CustomDescription
	
CMPerson class >> customDescription: aContainer 
	CustomDescription := aContainer``````language=smalltalkCMPerson class >> initialize
	super initialize.
	self customDescription: MAContainer new
	
CMPerson >> customDescription
	^ self class customDescription```	```language=smalltalkCMPerson >> magritteDescription
	^ super magritteDescription copy
		addAll: self customDescription; yourself```Execute: `CMPerson initialize` Note that we add a customise element shared by all the instances but we could do the same at the instance level. Now when working with table it is mandatory that all the rows have the same description. ### Exercise 19 Add another link above your report to let the user edit the custom description. Unfortunately the default implementation of MADescriptionEditor has no way to go back to the caller. The simplest solution is to subclass `MADescriptionEditor` and render an additional button by overriding `renderButtonsOn:` that answers the modified description.  #### Solution 19```language=smalltalkMADescriptionEditor subclass: #MAPersonDescriptionEditor
	instanceVariableNames: ''
	classVariableNames: ''
	package: 'Magritte-Tutorial'``````language=smalltalkMAPersonManager >> modifyPersonModel
	| description |
	description := self call: (MAPersonDescriptionEditor new
		magritteDescription: CMPerson customDescription). 
	description isNil
		ifFalse: [ CMPerson customDescription: description ]``````language=smalltalkMAPersonManager >> renderContentOn: html
	html heading: 'Person Manager'.
	#( modifyPersonModel add filter clear )
		do: [ :symbol | html anchor on: symbol of: self ]
		separatedBy: [ html space ]. 
	html render: self report``````language=smalltalkMAPersonDescriptionEditor >> renderButtonsOn: html 
	super renderButtonsOn: html.
	html submitButton
		on: #save of: self```Now you can add and save a new and specific description representing a new object.SD: Should check with the version of magritte that does not break on XML...