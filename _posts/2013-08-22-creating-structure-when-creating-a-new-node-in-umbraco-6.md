---
layout: post
title: Creating Structure When Creating a New Node in Umbraco 6
category: posts
---

## The situation

Umbraco is an amazing CMS that let's you define your own data, structure it your way and display it however you want using templates.

I've built several websites in the past with recipe sections. A recipe could have multiple lists of ingredients or multiple lists of instructions.

You could just have one or two rich-text editor properties and maybe it will work out, but if you are trying to control the output on the templates you want more control over the input.

Using additional document types which define properties for ingredients and directions I end up using the following structure:

	Recipe doctype
		Ingredients doctype (for organization)
			Ingredients List doctype
		Directions doctype (for organization)
			Directions List doctype

## The problem

The problem with the above setup is that it requires a user to create multiple nodes whenever they want to create a recipe. 

My original solution was to just create a unpublished recipe node with the sub structure and just copy it as needed.

While working on a recent project I explored the idea of automatic node creation.

## A solution

Umbraco has an events system that let's you wire up your own actions whenever something happens.

For the above I was interested in *when a specific document type is created: create additional nodes.*

Start by creating a new class file in the App_Code folder. It will extend ApplicationEventHandler.

{% highlight csharp linenos %}using System;using Umbraco.Core;using Umbraco.Core.Events;using Umbraco.Core.Models;using Umbraco.Core.Services;
public class NewRecipeNodeEventHandler : ApplicationEventHandler
{    public NewRecipeNodeEventHandler()    {        ContentService.Created += ContentService_Created_Recipe;    }    private void ContentService_Created_Recipe(IContentService sender, NewEventArgs<IContent> e)    {        if (e.Entity.ContentType.Alias.Equals("Recipe", StringComparison.CurrentCultureIgnoreCase))        {            try            {                var ingredients = sender.CreateContent("Ingredients", e.Entity.Id, "Ingredients");                sender.Save(ingredients);                var ingredientList = sender.CreateContent("List 1", ingredients.Id, "IngredientList");                sender.Save(ingredientList);                var directions = sender.CreateContent("Directions", e.Entity.Id, "Directions");                sender.Save(directions);                var directionsList = sender.CreateContent("List 1", directions.Id, "DirectionList");                sender.Save(directionsList);            }            catch (Exception)            {            }        }}
{% endhighlight %}

The code starts by assigning our own event function to Umbraco's ContentService.Created event handler.

When a new document is created ContentService_Created_Recipe is called and starts by checking the alias of the created document and if it matches create multiple new nodes, saving them along the way. 

Since this could fail if a new content alias doesn't exist I wrapped the content creation code in a try/catch just so the nothing breaks in Umbraco. 

I have no idea if this can be improved upon or if there are any potential problems but it is working out fine for now.

References:

1. [ContentService Created Event - Entity doesn't have ID](http://our.umbraco.org/forum/developers/extending-umbraco/38477-ContentService-Created-Event-Entity-doesnt-have-ID)
2. [Default values for the properties of you document types](http://www.vanarkel.net/umbraco-cms/default-values.aspx)
3. [Attaching Document Event Handlers](http://our.umbraco.org/wiki/reference/api-cheatsheet/attaching-document-event-handlers)

