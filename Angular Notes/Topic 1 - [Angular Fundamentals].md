Q1. What is Angular ?
Angular is a typescript based front end framework developed by Google for building Single page application (SPA).

Key Points:
1. Open Source.
2. Component based architecture.
3. Uses Typescript
4. Built in dependency injection.
5. Uses RxJS for reactive programming.
6. Follows MVC like structure.

Q2. What is SPA.
SPA loads a single HTML page and dynamically updates content without reloading the entire page.
Traditional Web App:
Every click -> full page reload.

SPA:
Initial load -> After that only data changes.
Advantages:
1. Faster user experience
2. Less server load
3. Smooth transitions.
4. Better Performance.
In Angular:  
Routing enables SPA behavior.

Q3. Angular Architecture.
Main building blocks:
1. Modules.
2. Components.
3. Templates.
4. Services.
5. Dependency Injection.
6. Routing.

Flow:  
Module → Component → Template → User Interaction → Service → API → Response → Update View

Q4. Common CLI commands.
Ans. ng new project-name -> Create Project.
ng serve -> Run Project.
ng g c component-name -> generate new component.
ng build  -> build production.

Q5. NgModule.
Ans. Properties: 
1. declarations (components, directives, pipes)
2. imports (other modules)
3. providers (services)
4. bootstrap (starting component)
Root module is AppModule.
why modules?
-> Organize features.
-> Lazy loading.
-> Better maintainability.

```
@NgModule({
  declarations: [
    AppComponent,
    FeatureComponent // Add new components, directives, pipes here
  ],
  imports: [
    BrowserModule
    // Add other modules here (e.g., FormsModule, HttpClientModule, FeatureModule)
  ],
  providers: [
    // Add services here
  ],
  bootstrap: [AppComponent] // The root component to bootstrap the application
})
```

Q6. Components 
Components = Basic building block of Angular.
Each component has:
1. Typescript file .ts
2. HTML file .html
3. Css file 
4. Decorator (@Component)
Example structure

@Component({
selector: 'app-user',
templateUrl: './user.component.html',
styleUrls: ['./user.component.css']
})

Q7. Templates.
Templates define the view(UI).

Q8. Data Binding (very important)
Ans. Data binding connects component (.ts) with template (.html)

Types.
1. Interpolation -> {{variable}}
2. Property binding -> [property] = "value"; [Example:  <img [src] = "imageUrl"> ]
3. Event Binding (event) = "method()" Example :: <button (click)="save()"> Save </button>
4. Two way binding: 
		[(ngModel)]="variable" combines property + event binding


Q9. Directives
Ans. Directives modify DOM behavior.
Types: 
1. Structural Directives (structural uses star(*) symbol)
	Change DOM structure.
	 *ngIf
	 *ngFor
	 *ngSwitch
2. Attribute Directives.
		Change appearance/behaviour
		ngClass & ngStyle

Q10. Pipes.
Ans. Pipes transform data in template.
Built in pipes.
1. date
2. uppercase
3. lowercase
4. currency
5. percent
6. join

Custom Pipes:
you can create your own pipe using @Pipe decorator.

Pure vs impure pipes:
Pure => runs only when input changes.
Impure => Runs every change detection cycle.

**Data Binding**

1. Interpolation
2. Property binding
3. Event binding
4. Two-way binding
5. How Angular change detection connects everything
