---
layout: post
title: "Requirements For a Website Platform"
id: 4cf5b685dabe9d2c6100073e
updated: 2010-11-30T21:44:21-05:00
date: 2005-10-22T20:37:00-04:00
redirect_from: /blog/archives/2005/10/22/requirements-for-a-website-platform/
---

<p>
I've struggled for a long time to find a blogging and wiki package that fits my needs. It seems that there are plenty of packages out there that are intended for small, single user blogs, and several packages aimed at large community websites. The problem is finding one that fits a smaller website that is more than a blog, but not a community portal. For the time being, I've settled on <a href="http://www.wordpress.org">WordPress</a>. It's nowhere near ideal, but it will work for now.

</p>
<p>
As I've evaluated different packages, the lines between a blog, wiki, CMS, and forum have become blurred. The only real difference in them is the workflow of creating posts, and how the posts are presented. If designed properly, I think the same backend and data model could power at least those content tools that I listed, and possibly more. Some CMS products like <a href="http://www.joomla.org/">Joomla</a> (formerly known as Mambo) have done this to a degree, but they've also made it overy complex in the process.

</p>
<p>
As I've been evaluating different packages in the past few months, I've started gathering a list of requirements that the ideal website platform would implement. I've listed them below&mdash;in no specific order&mdash;and I'd like to continue to refine them. I've also started to hack out a design, but it has a long ways to go.

</p>
<h2>
Requirements

</h2>
<ul>
<li>
<h2>
Interface

</h2>
<p>
The most important word to describe the interface is "SIMPLE"! If anything jeopordizes simplicity, it should be redone. It should be as easy for non-technical users as it is for techies. It shouldn't take longer than 10 minutes to train someone to use it.

</p>
<p>
It also shouldn't appear to be a wiki or a blog to the anonymous user. There should not be any visible login or registration links.

</p>
</li>
<li>
<h2>
Design Customization

</h2>
<p>
One of the most frustrating experiences in my wiki search so far has been the difficulty in customizing the interface. One should be able to completely customize the templates using only a couple of simple files. Additionally it would be nice if templates could be created in a variety of markup and templating languages such as the native scripting language (e.g. PHP, JSP, Ruby, etc.), a templating engine (Smarty, Velocity, Freemarker, etc.), and XSLT.

</p>
<p>
Templates should also contain different "flavors," or pieces of the template that are displayed for different types of pages. For example, a blog page would look different than an article, or an article listing.

</p>
<p>
Templates should also support themes, or "skins." Themes would be implemented using CSS.

</p>
</li>
<li>
<h2>
Request Processing

</h2>
<p>
The biggest problem with existing packages really has nothing to do with their features, data model, or ease of use. They fall short due to rigitity or lack of customization in the way that they present the data and process requests. There has to be a way to create a more configurable request processing mechanism. Different requests processors should allow the software to look and act more like a blog, a wiki, both, or anything else imaginable. The request processor would control how the URLs appear&mdash;which, oddly enought, is actually a really important issue&mdash;and the navigation between posts.

</p>
</li>
<li>
<h2>
Attachments

</h2>
<p>
Files should be able to be attached to any post or comment (at the authors discretion). Attachments should be able to be referenced from within the pages and comments that they're attached to.

</p>
</li>
<li>
<h2>
Storage

</h2>
<p>
With the great <acronym title="object-relational mapping">ORM</acronym> and database abstracton tools available today, there's no excuse not to support a variety of databases. Beyond that, a good design should allow you to choose between a relational database and a file backed database.

</p>
</li>
<li>
<h2>
Permissions

</h2>
<p>
CMS, wikis, and blogs seem to mostly take an all or nothing approach to permissions. You're either in or you're out; everyone can read your post, or no one can. <a href="http://www.cowiki.org">CoWiki</a> has the best solution that I've seen and immitates unix-style permissions and groups. This is a simple and proven approach, and significantly less complex than the other solutions I've seen. A close second is <a href="http://atlassian.com/software/confluence/">Confluence</a>. It uses a similar owner-group-anonymous style approach, but has a greater variety of permissions to assign.

</p>
</li>
<li>
<h2>
Input

</h2>
<p>
Multiple forms of input should be supported, including inline markup editor (wiki or XHTML) and known blogging and XMLRPC APIs
such as Blogger, MetaWeblog, MovableType, and Atom.

</p>
<p>
Ajax is a beautiful thing if used tastefully. What better use for it than to allow editing in place. Authors should be able to edit and add content on the pages without having to refresh the page or go into an admin interface.

</p>
<p>
The markup editor should support multiple markup syntaxes including plain XHTML, an XHTML rich text editor, and multiple flavors of wiki syntaxes. It should be easy to add additional wiki syntaxes by modifying a properties file.

</p>
</li>
<li>
<h2>
Import

</h2>
<p>
Anyone that has created something new knows that the best way to gain users is to remove the cost of entry. User should have the ability to import their entries from existing wiki and blog applications. What better way to demo a new product than to import your own data play around with it?

</p>
</li>
<li>
<h2>
Plugins

</h2>
<p>
This is almost a given, but a rich plugin API should be available that allows plugins to provide processing logic, modify content, modify the interface, or add functionality. As with the import functionality, the best way to gain users is to remove the cost of entry. How great would it be to implement the plugin API of popular packages, allowing their plugins to work flawlessly? Without plugins, most packages really have no advantage over the next one. Implementing the plugin API of other packages would give you at a minimum, the same features that it has. This obviously would be constrained by language bariers, but it would at least provide compatability with leading packages in the respective language.

</p>
</li>
<li>
<h2>
Output

</h2>
<p>
Besides the obvious output types of XHTML and RSS/ATOM, it would be great to get output in RDF and PDF. It should be as easy to export data as it is to import. This would make the wiki a great tool for generating documentation.

</p>
<p>
It would also be nice to be able to customize feeds, with options such as showing excerpts only, showing comments, and selecting only specific categories.

</p>
</li>
<li>
<h2>
Language

</h2>
<p>
This is more of an anti-requirement. At this point, I don't care what it is written in. I think Java will provide the greatest flexability in design and privide a more scalable (caution: over used buzz-word) base, but it also poses the greatest risk of bloat. Ruby on Rails is new, exciting and quick. I've only tasted the koolaid, though, so I'm still not convinced that it will solve all my problems. PHP is a candidate, especially since it <a href="http://developers.slashdot.org/article.pl?sid=05/10/21/1240258">is succeeding where Java isn't</a> (glad that is someone's experience, because it hasn't been mine). I've coped with my dissappointment with PHP5's half-hearted attempt at being an useful OO language, so I'd be willing to give it another chance. About the only requirements for the language is that it has to be able to be developed using free (as in freedom) tools, run on a free platform, and have a bright future (sorry Perl).

</p>
</li>
</ul>
