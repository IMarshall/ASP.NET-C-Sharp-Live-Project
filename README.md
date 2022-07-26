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
- [x] [Create a method to convert uploaded images to a byte array](https://github.com/IMarshall/ASP.NET-C-Sharp-Live-Project/blob/main/README.md#image-to-byte-array) and save them in the database. Call the method from the controller when formdata is posted from create and edit pages.
- [x] [Create a method to convert byte arrays from the database to images to be shown on the page.](https://github.com/IMarshall/ASP.NET-C-Sharp-Live-Project/blob/main/README.md#byte-array-to-image) Call the method using Razor in the view.
- [x] [Fix edit page bug so that a new image doesn't have to be uploaded every time.](https://github.com/IMarshall/ASP.NET-C-Sharp-Live-Project/blob/main/README.md#using-tempdata-to-fix-edit-page)
- [x] Style the create and edit pages using Bootstrap and custom CSS/cshtml. Pages should be responsive and change from two columns to one column when screen size is small.
- [x] Style the index page so that each photo and it's properties are represented by a Bootstrap card. Organize photos by the production that they belong to.
- [x] Add a search bar and paging to the index page. Search terms should carry over from page to page until cleared from the search bar.
- [x] [Restrict access to CRUD pages to users with the Admin role.](https://github.com/IMarshall/ASP.NET-C-Sharp-Live-Project/blob/main/README.md#restricting-access-to-admin-account) Anonymous users should only be able to view the index page.
### Demo Video

https://user-images.githubusercontent.com/96323360/180877079-7101eb1f-a0f1-4e46-98fd-a577847c72c1.mp4

### Image Preview Before Submission to Database
On the create and edit pages, the users can upload a photo to be stored in the database. I wanted to allow them to preview the photo before it was actually submitted so I made a separate image element which would have it's display attribute set to hidden until a photo was uploaded. I set the onchange attribute of the file input element to trigger a JavaScript function. 

`<input id="imgInput" type="file" accept="image/*" name="postedFile" onchange="showPreview()" />`

That function checks to make sure a file was uploaded, uses URL.createObjectURL to create a URL for that file, sets the source of my separate image element to that URL, and finally sets the display attribute of the image element to block, so that it shows on the page.

```
function showPreview() {
    imgInput = document.getElementById("imgInput");
    imgPreview = document.getElementById("imgPreview");
    imgContainer = document.getElementById("imgContainer");

    const [file] = imgInput.files;
    if (file) {
        imgPreview.src = URL.createObjectURL(file)
    }

    imgPreview.style.display = "block";
    imgContainer.style.border = "2px solid var(--main-color)";
    imgContainer.style.outline = "2px solid var(--secondary-color)";
    imgPreview.alt = "Your photo"
}
```
### Converting Images and Byte Arrays
In order to store the photos in the database, I needed to be able to convert them to a byte array, and then convert them back to an image each time they needed to be displayed in the view. I didn't have any experience with this so this was a great learning opportunity. I wrote a few short methods to do the conversions and called them whenever needed.
#### Image to Byte Array
```
public byte[] ConvertPhoto(HttpPostedFileBase postedFile)
{
    byte[] bytes;
    using (BinaryReader br = new BinaryReader(postedFile.InputStream))
    {
        bytes = br.ReadBytes(postedFile.ContentLength);
    }
    return bytes;
}
```
I called this method from the controller whenever an image was submitted through an HTTP POST request.
`productionPhoto.PhotoFile = ConvertPhoto(postedFile);`
#### Byte Array to Image
```
public ActionResult ConvertBytes(int? id)
{
    if (id == null)
    {
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    ProductionPhoto productionPhoto = db.ProductionPhotoes.Find(id);
    if (productionPhoto == null)
    {
        return HttpNotFound();
    }
    return File(productionPhoto.PhotoFile, "image/png");
}
```
I called this method from the view whenever I needed to display a photo.
```
string source = Url.Action("ConvertBytes", "ProductionPhotos", new { id = item.ProductionPhotoId });
<img class="card-img-top" src="@source" alt="@item.Description" style="width: 100%; height: 15vw; object-fit: cover;">
```
### Using TempData to Fix Edit Page
Getting the edit page functionality to work properly was a challenge that I didn't anticipate at first, but it helped me become more familar with the MVC design pattern. The issue that I ran into was that if I changed a property, but didn't upload a new photo, an exception would get thrown in the controller because the file input was returning the PhotoFile property as null. Here's what the code in my view looked like:
```
<div class="form-group">
    @Html.LabelFor(model => model.PhotoFile, htmlAttributes: new { @class = "col-form-label-lg" })
    <div class="prodphoto-center">
        <div class="prodphoto-img-container" id="imgContainer">
            <div class="prodphoto-img-preview">
                @{string source = Url.Action("ConvertBytes", "ProductionPhotos", new { id = Model.ProductionPhotoId });}
                <img id="imgPreview" src="@source" alt="@Model.Description" style="display: block"/>
            </div>
            <label for="imgInput" class="col-form-label-lg">Replace Image</label>
            <input id="imgInput" type="file" accept="image/*" name="postedFile" onchange="showPreview()" />
        </div>
    </div>
</div>
```
The file input needed to be there so that a user could replace the photo if they wanted to, but I had to figure out how return the original photo to the controller if they chose not to. I originally thought to set the default value of the input element to the original photo, but quickly found out that inputs with the type attribute set to file can't have a default value. My next thought was to check in the controller if the returned file input was null and just ignore it if that was the case. That obviously got rid of the exception being thrown, but still updated the property in the database which replaced the existing byte array with an empty one. The solution I eventually settled on was to pass the original photo back to the controller using TempData. Here's the short section of code that I added to the view:
```
@{
    var base64 = Convert.ToBase64String(Model.PhotoFile);
    TempData["bytes"] = base64;
}
```
And here's the logic that I put in the controller to add that original byte array back to the data base if no new image was uploaded.
```
if (postedFile == null)
{
    byte[] bytes = Convert.FromBase64String(TempData["bytes"].ToString());
    productionPhoto.PhotoFile = bytes;
}
else
{
    productionPhoto.PhotoFile = ConvertPhoto(postedFile);
}
```
### Restricting Access to Admin Account
I was asked to restrict access to CRUD functionality to only users who had an admin role. I did this by adding the `[AllowAnonymous]` or `[Authorize]` to each method. I also wrote a short method in my startup.cs file to create an "Admin" role and assign it to a default admin user if one didn't already exist. This method was called from the Configuration method so that it would run on startup.
```
private void CreateRoles()
{
    ApplicationDbContext db = new ApplicationDbContext();
    
    var userManager = new UserManager<ApplicationUser>(new UserStore<ApplicationUser>(db));
    var roleManager = new RoleManager<IdentityRole>(new RoleStore<IdentityRole>(db));

    if (!roleManager.RoleExists("Admin"))
    {
        var role = new IdentityRole();
        role.Name = "Admin";
        roleManager.Create(role);
        var user = new ApplicationUser()
        {
            UserName = "admin",
            Email = "test@test.com"
        };
        string password = "P@ssw0rd";
        var checkUser = userManager.Create(user, password);
        if (checkUser.Succeeded)
        {
            var result = userManager.AddToRole(user.Id, "Admin");
        }
    }
}
```