# Server side and Client side Validations

## Client side validations

Happen in the Clients Browser and are fast because there is no trip back to the Server.

Can easily be manipulated in the Browser.

This is fine for User feedback but not good for security reasons.

## Server side validations

These happen in the Server (back end code).

Slower than Client side validations because they are sent to the server.

Safe and secure because the Client cannot change these validations.

Should be implemented regardless of any Client side validations.

## Server side validations for the Register page

As an example if we open the Register page and submit it without adding any user information we will end up with a nasty Form error and we see an exception.

This gives the user unwanted information. Let's add some validation and the first type of validation would be the ``Required`` annotation on our Model.

Go to the ``Register.cshtml.cs`` code class.

Note that we are using the ``RegisterViewModel``. Go to the Model.

Add the ``Required`` annotation to each field.

```bash
    public class Register
    {
        [Required]
        public string Username { get; set; }

        [Required]
        public string Email { get; set; }

        [Required]
        public string Password { get; set; }
    }
```

Now back in the ``Register.cshtml.cs`` class we need to check for validation in the ``OnPost()`` method. We use the ``ModelState`` to do this.

```bash
    public async Task<IActionResult> OnPost()
    {
        if (ModelState.IsValid)
        {
            var user = new IdentityUser
            {
                UserName = RegisterViewModel.Username,
                Email = RegisterViewModel.Email
            };

            var result = await userManager.CreateAsync(user, RegisterViewModel.Password);

            if (result.Succeeded)
            {
                var rolesResult = await userManager.AddToRoleAsync(user, "User");

                if (rolesResult.Succeeded)
                {
                    ViewData["Notification"] = new Notification
                    {
                        Type = Enums.NotificationType.Success,
                        Message = "User registered successfully!"
                    };

                    return Page();
                }
            }

            ViewData["Notification"] = new Notification
            {
                Type = Enums.NotificationType.Error,
                Message = "User NOT registered!"
            };

            return Page();
        }
        else
        {
            return Page();
        }
    }
```

We add the ``ModelState.IsValid`` statement that is a boolean value. If ``true`` then the model is valid.

Note that we wrap the ``if`` statement around all of our code that is required to submit our data. This creates an error so we need to create an ``else`` block that finally returns the page if there are problems.

This will stop us from submitting an invalid Form. It would be better if we could display some more appropriate messages to the user.

We can add messages to the ``Register.cshtml`` page.

Inside the Form post area we have our fields we can add span elements to show our messages.

```bash
<div class="mb-3">
    <label class="form-label">Username</label>
    <input type="text" class="form-control" asp-for="RegisterViewModel.Username" />
    <span class="text-danger" asp-validation-for="RegisterViewModel.Username"></span>
</div>

<div class="mb-3">
    <label class="form-label">Email</label>
    <input type="email" class="form-control" asp-for="RegisterViewModel.Email" />
    <span class="text-danger" asp-validation-for="RegisterViewModel.Email"></span>
</div>

<div class="mb-3">
    <label class="form-label">Password</label>
    <input type="password" class="form-control" asp-for="RegisterViewModel.Password" />
    <span class="text-danger" asp-validation-for="RegisterViewModel.Password"></span>
</div>
```

Now run the page and don't add any text to the fields. We should get this result.

![Client side validation](assets/images/client-side-validation.jpg "Client side validation")

We are getting some error validation but there is more that we can do. The Email field must be a valid email address. We can't just add a number of characters and expect them to be a valid Email address.

We have some Client side validation with the Input type being ``email``.

```bash
    <input type="email" class="form-control" asp-for="RegisterViewModel.Email" />
```

![Email validation error](assets/images/wrong-email-address.jpg "Email validation error")

We can add Server side validation in the Model.

```bash
    [Required]
    [EmailAddress]
    public string Email { get; set; }
```

If we put the wrong data in the ``Email`` field we will get this error.

We also have a size limit on the number of characters in our ``Password`` field. In the ``Program.cs`` file we have already set some limits.

```bash
    builder.Services.Configure<IdentityOptions>(options =>
    {
        // Default password settings
        options.Password.RequireDigit = true;
        options.Password.RequireNonAlphanumeric = true;
        options.Password.RequireUppercase = false;
        options.Password.RequiredLength = 6;
    });
```

We can also add an annotation to force a minimum Password length in the Model.

```bash
    [Required]
    [MinLength(6)]
    public string? Password { get; set; }
```

With these annotations we have validated our fields on the Server side.

## Client side validations for he Register page

It is important to add the Server side validation **before** you add the Client side validation.

We have our three fields in the ``Register.cshtml`` page. We can add the ``required`` annotation to each field and with the ``Password`` we can add a minimum length attribute.

```bash
<div class="mb-3">
    <label class="form-label">Username</label>
    <input type="text" class="form-control" asp-for="RegisterViewModel.Username" required />
    <span class="text-danger" asp-validation-for="RegisterViewModel.Username"></span>
</div>

<div class="mb-3">
    <label class="form-label">Email</label>
    <input type="email" class="form-control" asp-for="RegisterViewModel.Email" required />
    <span class="text-danger" asp-validation-for="RegisterViewModel.Email"></span>
</div>

<div class="mb-3">
    <label class="form-label">Password</label>
    <input type="password" class="form-control" asp-for="RegisterViewModel.Password" required minlength="6" />
    <span class="text-danger" asp-validation-for="RegisterViewModel.Password"></span>
</div>
```

I now run the Form with valid ``Username`` and ``Email`` fields. Add 3 characters into the ``Password`` field.

![Minimum length error](assets/images/minimum-length-password.jpg "Minimum length error")

Using these attributes says that we have finished our Client side validation.

## Validating the Login page
