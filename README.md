# ASP.NET-C-Sharp-Live-Project
A repository to document what I learned and did during a 2-week sprint in the Tech Academy Software Developer Bootcamp.
## Introduction
During my software development course at the Tech Academy, I participated in a 2-week code sprint during which I was put in charge of creating a section of a large website built using ASP.NET MVC and Entity Framework. The project was an interactive website for managing the content and productions for a theater/acting company. I was tasked with building a section of the website where a website administrator could perform basic CRUD operations on photos for each production. I designed both the front-end and back-end for each page. The front end was developed using Razor, Bootstrap, HTML, CSS, and JavaScript. The back-end was developed using .NET MVC, Entity Framework, and C#. During the sprint, I worked with a team of other students and instructors, using Agile Methodologies to manage the project as a whole. DevOps for the project was done through Azure DevOps. Git was used for version control.
## Task List
Here's a short list of the different tasks that I completed throughout the live project:
- [x] Create Entity Model for Production Photos class.
- [x] Scaffold CRUD pages and add shared .cshtml layout to each.
- [x] Link the Productions model to the Production Photos model using a one-to-many fully defined relationship. Photos should be assigned to a Production when they are created.
- [x] Add image preview to create and edit pages that will display an uploaded image before the form is submitted.
- [x] Create a method to convert uploaded images to a byte array and save them in the database. Call the method from the controller when formdata is posted from create and edit pages.
- [x] Create a method to convert byte arrays from the database to images to be shown on the page. Call the method using Razor in the view.
- [x] Fix edit page bug so that a new image doesn't have to be uploaded every time.
- [x] Style the create and edit pages using Bootstrap and custom CSS/cshtml. Pages should be responsive and change from two columns to one column when screen size is small.
- [x] Style the index page so that each photo and it's properties are represented by a Bootstrap card. Organize photos by the production that they belong to.
- [x] Add a search bar and paging to the index page. Search terms should carry over from page to page until cleared from the search bar.
- [x] Restrict access to CRUD pages to users with the Admin role. Anonymous users should only be able to view the index page.
### Demo Video

### Converting Images and Byte Arrays

### Using TempData to Fix Edit Page

### Styling Index Page

### Restricting Access to Admin Account
