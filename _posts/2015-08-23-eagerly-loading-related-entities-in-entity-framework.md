---
layout: post
title: Eagerly loading related entities in Entity Framework
categories: Entity Framework
tags:
- C#
- eager loading
- Entity Framework
- explicit loading
- lazy loading
---

<p>Recently, while working on a project, using <strong>Entity Framework</strong> I felt the need to figure out a way to <strong>eagerly load related entities</strong> in the data layer.<br />
While I was trying to find a way to achieve this, I got to know that Entity Framework provides <strong>3 ways</strong> to load related entities...</p>

<ol>
<li>Eager</li>
<li>Lazy</li>
<li>Explicit</li>
</ol>

<h6>Example:</h6>

<p>Consider the entities Post and Category that are part of a blog.</p>

<p><img src="http://sundeepkamath.in/wp-content/uploads/2015/08/Post_Category.png" alt="Entities Post and Category" /></p>

<p>The post entity could have a reference to the category associated with it.<br />
Similarly the Category entity could have a collection of posts that it is associated with.</p>

<p>When we want these referenced entities to be lazy loaded we mark them as <strong>virtual</strong>.<br />
This <strong>virtual</strong> keyword when used, loads the data of the corresponding related entity only when this <strong>navigation</strong> property is used.<br />
For the sake of explanation, I'll just consider the code snippet for the Category class here.</p>

{% highlight C# %}
public class Category
{
    public Category()
    {
        this.Posts = new List&lt;Post&gt;();
    }

    public int Id { get; set; }

    [Required]
    [MaxLength(50)]
    public string Name { get; set; }

    [MaxLength(200)]
    public string Description { get; set; }

    //The Posts property collection is loaded lazily
    public virtual ICollection&lt;Post&gt; Posts { get; set; }
}
{% endhighlight %}

<p>As can be seen the Posts property is marked as virtual. Hence, the data in this property <strong>would be loaded only when this property is accessed in code.</strong></p>

<p>If however we wish to load these related entities right at the beginning (or eagerly) when the parent entity is being loaded we can achieve the same using the <strong>Include</strong> extension method.<br />
To use this method we would need to import the <strong>System.Data.Entity</strong> namespace.</p>

{% highlight C# %}
using (var context = new BloggingContext()) 
{ 
    // Load all categories and related posts 
    var categories = context.Categories
                    .Where(c =&gt; c.Name == "C#")
                    .Include(c =&gt; c.Posts)
                    .ToList&lt;Category&gt;();
}
{% endhighlight %}

<p>There is an excellent <a href="https://msdn.microsoft.com/en-in/data/jj574232.aspx">article</a> on msdn that explains all the above mentioned ways of loading related entities in Entity Framework.<br />
I haven't used the Explicit loading method so far, but the msdn link I mentioned above explains this very well.<br />
Hope this helps you too.</p>
