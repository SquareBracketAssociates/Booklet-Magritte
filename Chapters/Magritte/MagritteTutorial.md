## Magritte Katas
	owner: 'Magritte'
	project: 'Magritte3'
	user: ''
	password: ''
    instanceVariableNames: 'title firstName lastName homeAddress officeAddress picture birthday phones password'
    classVariableNames: ''
    package: 'Magritte-ContactManager'
    instanceVariableNames: 'kind number'
    classVariableNames: ''
    package: 'Magritte-ContactManager'
    instanceVariableNames: 'street plz place canton nationality'
    classVariableNames: ''
    package: 'Magritte-ContactManager'
    ^ comment 
    <magritteDescription>
    ^ MAStringDescription new
            accessor: #comment; 
            label: 'Comment';
            priority: 100 ; 
            yourself
    <magritteDescription>
     ^ MAMemoDescription new
                 accessor: #comment; 
                 label: 'Comment';
                 priority: 100 ; 
                 yourself
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
            yourself
    <magritteDescription>
     ^ MAStringDescription new
            accessor: #email;
            label: 'Email';
            priority: 95;
            yourself
    <magritteDescription>
    ^ (MAStringDescription new
        accessor: #email;
        label: 'Email';
        priority: 95;
        yourself)
        addCondition: [ :value | value matches: '#*@#*.#*' ] labelled: 'Please enter a valid email';
        addCondition: [ :value | (value matches: '#*@hotmail.com') not ] labelled: 'Hotmail users not allowed';
        addCondition: [ :value | (value matches: '#*@gmx.#*') not ] labelled: 'GMX users not allowed';
        beRequired; yourself
    <magritteDescription>
    ^ CMPerson new descriptionNationality copy
        label: 'Country'; 
        yourself
    instanceVariableNames: ''
    classVariableNames: 'Persons'
    package: 'Magritte-ContactManager'
    "self initialize"
    
    super initialize.
    self persons: OrderedCollection new.
    WAAdmin register: self asApplicationAt: 'personmanager'
    html heading: 'Person Manager'. 
    | person |
    person := self call: (CMPerson new asComponent addValidatedForm; yourself).
    person isNil
        ifFalse: [ self class persons add: person ]
    html heading: 'Person Manager'.
    html anchor
        callback: [ self add ]; with: [ html text: 'add']
    ^ self class persons
    ^ Persons
    html heading: 'Person Manager'. 
    html anchor
        callback: [ self add ];
        with: [ html text: 'add' ]. 
    html break.
    self persons
        do: [ :person | self renderLineForPerson: person on: html ] separatedBy: [ html break ]
    html 
        text: aPerson firstName; 
        space; 
        text: aPerson lastName
    self call: (aPerson asComponent addValidatedForm; yourself)
    html anchor
        callback: [ self editPerson: aPerson ];
        with: [ html text: aPerson firstName; 
                    space; 
                    text: aPerson lastName ]
    self call: (aPerson asComponent 
        addForm: #(cancel); 
        readonly: true; 
        yourself)
    html anchor
        callback: [ self editPerson: aPerson ];
        with: [ html text: aPerson firstName; 
                        space; 
                        text: aPerson lastName ].
    html space.
    html anchor
        callback: [ self viewPerson: aPerson ];
        with: [ html text: 'view' ].
    instanceVariableNames: 'report'
    classVariableNames: 'Persons'
        category: 'Magritte-ContactManager'
    ^ report
MAPersonManager >> report: aReport 
    report := aReport    
    super initialize.
    self report: (MAReport rows: self persons description: CMPerson new magritteDescription)
    ^ OrderedCollection with: self report
    html heading: 'Person Manager'. 
    html anchor on: #add of: self. 
    html render: self report
    | person |
    person := self call: (CMPerson new asComponent addValidatedForm; yourself).
    person isNil 
        ifFalse: [ 
            self persons add: person.
            self report refresh ]

    ^ aPerson magritteDescription select: [ :each | 
         #(firstName lastName) includes: each accessor selector ] 
    super initialize.
    self report: (MAReport rows: self persons description: (self  filteredDescriptionsFrom: CMPerson new))
	super initialize.
	self report: (MAReport rows: self persons description: (self  filteredDescriptionsFrom: self persons first)).
	self report addColumn: (MACommandColumn new 
						addCommandOn: self selector: #viewPerson:; 
						addCommandOn: self selector: #editPerson: text: 'Edit'; yourself)
	| filter |
	filter := self request: 'Enter a filter string'. 
	self report rowFilter: [ :person |
		(self report columns collect: [ :col |
			col magritteDescription in: [ :desc | desc toString: (person readUsing: desc) ] ]) anySatisfy: [:value | value matches: filter ] ]
	col description in: [ :desc | desc toString: (person readUsing: desc) ] ])
	self report rowFilter: nil
	html heading: 'Person Manager'.
	#( add filter clear )
		do: [ :symbol | html anchor on: symbol of: self ]
		separatedBy: [ html space ]. 
	html render: self report
	^ CustomDescription
	
CMPerson class >> customDescription: aContainer 
	CustomDescription := aContainer
	super initialize.
	self customDescription: MAContainer new
	
CMPerson >> customDescription
	^ self class customDescription
	^ super magritteDescription copy
		addAll: self customDescription; yourself
	instanceVariableNames: ''
	classVariableNames: ''
	package: 'Magritte-Tutorial'
	| description |
	description := self call: (MAPersonDescriptionEditor new
		magritteDescription: CMPerson customDescription). 
	description isNil
		ifFalse: [ CMPerson customDescription: description ]
	html heading: 'Person Manager'.
	#( modifyPersonModel add filter clear )
		do: [ :symbol | html anchor on: symbol of: self ]
		separatedBy: [ html space ]. 
	html render: self report
	super renderButtonsOn: html.
	html submitButton
		on: #save of: self