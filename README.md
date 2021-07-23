# Root project details

### Table of Contents
1. [About](#About)
	1. Projects
	1. Blogs
2. [Structure](#Structure)
	1. [Authentication](#Authentication)
	2. [Database](#Database)
	3. [Blogs](#Blogs)
3. Maintenance
	1. Authentication
	1. Database
	1. Blogs

### Authentication Structure
Flow:
-> Site checks if authorization token is present as a cookie
-> If there is a cookie, get the profile
-> If no cookie is found, do nothing
-> When a user chooses to make a blog post, they must first be authenticated
		with one of the providers listed below. 
-> If their cookie is present, they will be shown their profile name and the
		button to reply to post.
-> If their cookie is not present, they will be shown the options to 
		authenticate with one of the providers listed below.
-> Once they have been authenticated, they will be directed back to the blog
		page to post their reply.

The authentication method used is [OAuth2.0](https://oauth.net/2/).

The authentication providers implemented:
- GitHub
- Google
- Twitter
- LinkedIn (in progress)
- Facebook (in progress)

If a user wishes to remove the token, they will be able to log out. The site
does not require the user to log in to view the content. A log in action 
will only be required if the user wishes to add their thoughts to the blog
posts. For this reason, a log in option in the nav bar is not required.

For now, continue to generate a public and private key pair
with setKey. The plan is to generate a private key that can 
be rotated when a user decides to remove their data and token
from the application without affecting the other users that 
have used the same token. 

Questions: 
- what kind of data table would best represent this functionality?
- Would it be a has table?

Using the following module provided by nodejs:
https://nodejs.org/api/crypto.html#crypto_crypto_generatekeypair_type_options_callback

### Database Structure
Blogpost structure:
```
	BlogPost: {
		id: serial pk,
		title: string,
		posted: date,
		modified: date,
		content: text
	}
```

Reply structure:
``` 	
UserPost: {
	blogID: BlogPostID,	
	blogTitle: BlogPostTitle,
	responseID: hash(user,time, etc),
	auther: String,
	date: Date,
	message: String,
	replies: [UserPost.responseID)],
}
```

### Blogs Structure
Considerations: 
The length of the post should provide the author with enough 
space to share their thoughts. Following the contributor guidelines
for StackOverflow, a post will be about 1500 words +/- 500. 

The average word length is about 5 (rounded). Let's be generous and
bump that up to 6. If the character count reaches 12000 before it 
reaches 2000 words, prevent additional text entries. 

Message length: 
2000 words OR 12000 characters
 
The posts should be protected from hate speech. Other than that, the
users will be free to post what they want. The creation of 
community guidelines will be made and displayed so each user knows
what to expect before posting. Hate speech will be clearly defined.

Users that violate these guidelines will not have their messages
removed. The messages containing hate speech will have the 
individual words censored out but the all other data will remain
about the author.
 
Steps for posting a message:
1.	User navigates to /blogs[id] and selects 'Reply' for the /blog/title o
		or existing reply.
2. 	Check for a access token in either a cookie or session.
3.	If there is a valid, unexpired token, enable the button 
		to make a post. Also display their avatar (if available) and username 
4. 	If there there is no valid token, get one from the auth endpoint
		and authenticate the user. When this is complete, enable the 
		button to make a post ( and display profile picture, name)
5. 	The user's name will be prefilled and their focus will be on 
		the message field. 
6.	The word count is checked at each change event, updating the user
		on how many words/chars they have left.
7.	Once they are done, they submit their post.
8.	Their post is saved to the database and checked for adherence to
		community guidelines.
9.	If it passes the guideline check, the message is posted to the
		message board. If it does not, it is still posted to the message
		board, with censored words and the user is emailed and the message
		is given a public label for hate speech. 

For now, the messages will not be checked prior to being publicly posted.
Once I finish this blog project, I will complete that task. For now, trust
people to an extent by first making them provide some identifying information
prior to posting with oauth2.

Creating and Posting my Blog:
I want to be able to manage blogs from a local host application. The database 
will be separated, running on its own secure server. 

This will give me the ability to backup and manage the existing blog posts 
that have been staticly inserted into my site at every /blog/title
This will require that my server and database are secure. 

Every blog post backup will auto increment for reference to join
tables with UserPosts.
