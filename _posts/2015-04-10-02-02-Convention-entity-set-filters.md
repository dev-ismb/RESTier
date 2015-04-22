---
layout: post
title: "2.2 Entity Set Filters"
description: ""
category: "2. Conventions"
---

Entity set filter convention helps plug in a piece of filtering logic for entity set. It is done via adding an `OnFilter[entity set name](IQueryable<T> entityset)` method to the domain class.

	1. The filter method name must be OnFilter[entity set name], ending with the target entity set name.
	2. It must be a private method on the domain class with [EnableConventions] attribute.
	3. It should accept an IQueryable<T> parameter and return an IQueryable<T> result where T is the entity type. 

Supposed that ~/AdventureWorksLT/Products can get all the Product entities, the below OnFilterProducts method will filter some Product entities by checking the ProductID.

{% highlight csharp %}
using Microsoft.Restier.Conventions;
using Microsoft.Restier.Core;
using Microsoft.Restier.EntityFramework;
using System.Data.Entity;
using System.Linq;
using System.Threading.Tasks;

namespace AdventureWorksLTSample.Models
{
    [EnableConventions]
    public class AdventureWorksDomain : DbDomain<AdventureWorksContext>
    {
        private IQueryable<Product> OnFilterProducts(IQueryable<Product> entitySet)
        {
            return entitySet.Where(s => s.ProductID % 3 == 0).AsQueryable();
        }
	}
}
{% endhighlight %}

Now some testings will show that:

	1. ~/AdventureWorksLT/Products will only get the Product entities whose ProductID is  3,6,9,12,15,... 
	2. ~/AdventureWorksLT/Products([product id]) will only be able to get a Product entity whose ProductID mod 3 results a zero. 