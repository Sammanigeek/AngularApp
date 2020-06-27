# AngularApp

==============================================
Author : Sammani Rathnayake
===============================================
angular :7.2.0
bootstrap:4.5.0
font-awesome :4.7.0

==============================================
steps
==============================================
01 . set up node environment

02. install angular cli 

03. create new project
	command : ng new AgularApplication
	create a branch : git checkout -b AngularApp
	
04. install bootstrap 
	command : npm install bootstrap
	modified the angular.json file :-
		-----------------------------------------------------------
		styles": [
            "node_modules/bootstrap/dist/css/bootstrap.min.css"
            
          ]
		----------------------------------------------------------
05.check the project status
	command : ng serve --open

06. generate main 3 components
	commands : 1. ng g c employee-create 
			   2. ng g c employee-edit  
			   3. ng g c employee-list

07. create a routing service for navigating between components
	command : touch app/app-routing.module.ts
	
08. set up HttpClient Service to communicate with Http remote server and Http client API
	modified the app.module.ts :-
		--------------------------------------------------------------
			import { HttpClientModule } from '@angular/common/http';
				@NgModule({
					imports: [
						HttpClientModule
						]
				})
		---------------------------------------------------------------		
09. create employee.ts and rest-api.service.ts files to handle CRUD operations
    commands : 1.ng g cl shared/Employee
					inside the employee.ts define data types within the Employee class
		------------------------------------------------------------------------------------------------	
					export class Employee {
   
							id: string;
							employee_name: string;
							employee_salary: string;
							employee_age: string;
							
						}
		------------------------------------------------------------------------------------------------
		       2.ng g s shared/rest-api.service.ts
					inside the rest-api.service.ts write down the CURD methods for consuming RESTful API
					
		--------------------------------------------------------------------------------------------------			
					// Http Options
					  httpOptions = {
						headers: new HttpHeaders({
						  'Content-Type': 'application/json'
						})
					  }  

					  // HttpClient API get() method => Fetch employees list
					  getEmployees(): Observable<Employee> {
						return this.http.get<Employee>(`http://dummy.restapiexample.com/api/v1/employees`)
						.pipe(
						  retry(1),
						  catchError(this.handleError)
						)
					  }

					  // HttpClient API get() method => Fetch employee
					  getEmployee(id): Observable<Employee> {
						return this.http.get<Employee>(`http://dummy.restapiexample.com/api/v1/employee/` + id)
						.pipe(
						  retry(1),
						  catchError(this.handleError)
						)
					   
					  }  

					  // HttpClient API post() method => Create employee
					  createEmployee(employee): Observable<Employee> {
						return this.http.post<Employee>('http://dummy.restapiexample.com/api/v1/create', JSON.stringify(employee), this.httpOptions)
						.pipe(
						  retry(1),
						  catchError(this.handleError)
						)
					  }  

					  // HttpClient API put() method => Update employee
					  updateEmployee(id, employee): Observable<Employee> {
						return this.http.put<Employee>('	http://dummy.restapiexample.com/api/v1/update/' + id, JSON.stringify(employee), this.httpOptions)
						.pipe(
						  retry(1),
						  catchError(this.handleError)
						)
					  }

					  // HttpClient API delete() method => Delete employee
					  deleteEmployee(id){
						return this.http.delete<Employee>('	http://dummy.restapiexample.com/api/v1/delete/' + id, this.httpOptions)
						.pipe(
						  retry(1),
						  catchError(this.handleError)
						)
						
					  }
			-------------------------------------------------------------------------------------------------------------------------------------
					and useRxJS to handle asynchronous operations and errors.
					
			------------------------------------------------------------------------------------------------------------------------------------		
					import { retry, catchError } from 'rxjs/operators';
					
					 // Error handling 
					  handleError(error) {
						 let errorMessage = '';
						 if(error.error instanceof ErrorEvent) {      
						   errorMessage = error.error.message;
						 } else {      
						   errorMessage = `Error Code: ${error.status}\nMessage: ${error.message}`;
						 }
						 window.alert(errorMessage);
						 return throwError(errorMessage);
					  }

			------------------------------------------------------------------------------------------------------------------------------------		

09. access HttpClient API from components
				I).for routing module for route service :
				------------------------------------------------------------------------------------------------------------------------------------		
								import { AppRoutingModule } from './app-routing.module';
				------------------------------------------------------------------------------------------------------------------------------------		
				II).for forms module :
				------------------------------------------------------------------------------------------------------------------------------------		
								import { FormsModule } from '@angular/forms';
				------------------------------------------------------------------------------------------------------------------------------------		
				III).import main 3 components :
				------------------------------------------------------------------------------------------------------------------------------------		
								import { EmployeeCreateComponent } from './employee-create/employee-create.component';
								import { EmployeeEditComponent } from './employee-edit/employee-edit.component';
								import { EmployeesListComponent } from './employees-list/employees-list.component';
				------------------------------------------------------------------------------------------------------------------------------------		
				IV).inside the @NgModule :
				------------------------------------------------------------------------------------------------------------------------------------						
								@NgModule({
								  declarations: [
									AppComponent,
									EmployeeCreateComponent,
									EmployeeEditComponent,
									EmployeesListComponent
								  ],
								  imports: [
									BrowserModule,
									AppRoutingModule,
									HttpClientModule,
									FormsModule
								  ],
								  providers: [],
								  bootstrap: [AppComponent]
								})
				------------------------------------------------------------------------------------------------------------------------------------		
								
10.in in employee-create.component.html
	create add employee form 

11. in employee-create.component.ts
	send HTTP POST Request in Angular to add a new employee 
	------------------------------------------------------------------------------------------------------------------------------------		
	@Input() employeeDetails = { employee_name: '', employee_salary: '', employee_age: '' }
	
	addEmployee(dataEmployee) {
    this.restApi.createEmployee(this.employeeDetails).subscribe((data: {}) => {
      this.router.navigate(['/employees-list'])
    })
  }
  ------------------------------------------------------------------------------------------------------------------------------------		
12.in employees-list.component.html
	create table to display employee list with edit and delete buttons
	
13. in employees-list.component.ts	
		use  RESTful API service by sending get() and delete() request
	------------------------------------------------------------------------------------------------------------------------------------		
		// Get employees list
		  loadEmployees() {
			return this.restApi.getEmployees().subscribe((data: {}) => {
			  this.Employee = data;
			})
		  }

		  // Delete employee
		  deleteEmployee(id) {
			if (window.confirm('Are you sure, you want to delete?')){
			  this.restApi.deleteEmployee(id).subscribe(data => {
				this.loadEmployees()
			  })
			}
		  }

	------------------------------------------------------------------------------------------------------------------------------------		
14. in employee-edit.component.html
	create edit employee form 
	
15. in employee-edit.component.ts
	send HTTP PUT Request in Angular to update current employee 
	------------------------------------------------------------------------------------------------------------------------------------		
	 // Update employee data
	  updateEmployee() {
		if(window.confirm('Are you sure, you want to update?')){
		  this.restApi.updateEmployee(this.id, this.employeeData).subscribe(data => {
			this.router.navigate(['/employees-list'])
		  })
		}
	  }

------------------------------------------------------------------------------------------------------------------------------------		
