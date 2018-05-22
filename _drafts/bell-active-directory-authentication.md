# Add Identity framework to ASP.NET project #

Install Microsoft.AspNet.Identity.EntityFramework
Install Microsoft.AspNet.Identity.Owin
Install Microsoft.Owin.Cors
Install Microsoft.AspNet.WebApi.Owin
Microsoft.Owin.Security
Microsoft.Owin.Security.Owin

![](/images/20161017-install-identity-framework.png)

Add `System.DirectoryServices` reference

![](/images/posts/20180503-system-directory-service.png)

# Add Role and UserRole mapping tables #

	-- role table
	create table [QatchAccess].[tbl_Role] (
		Id int not null identity,
		Name nvarchar(50) not null,
		Description nvarchar(max) null,
		primary key (Id)
	)
	-- user role mappings
	create table [QatchAccess].[tbl_UserRole](
		Id int not null identity,
		EmployeeNumber nvarchar(50) not null,
		Email nvarchar(100) not null,
		RoleId int not null,
		primary key (Id)
	)
	-- populate roles
	insert into [QatchAccess].tbl_Role (Name, Description) values ('REQUESTER', 'employee who requires a license');
	insert into [QatchAccess].tbl_Role (Name, Description) values ('APPROVER', 'employee who approves a license');
	insert into [QatchAccess].tbl_Role (Name, Description) values ('ADMINISTRATOR', 'employee who manages the system');

![](/images/posts/20180503-table-user-role.png)

	The role and user-role mapping data can be changed based on project requirements. The table structure is reusable.

# Add protected resource #
1. Add [Authorize] attribute to the controller. i.e.

		[RoutePrefix("api")]
	    [Authorize]
	    public class AccessRequestController : ApiController
		{
	 		[Route("max-license-count")]
	        [HttpGet]
	        public int GetMaxLicenseCount()
	        {
	            return 300;
	        }
		}

	Now, we will get 401 Unthorized error when trying to access the resource at `/api/max-license-count` directly.

1. 