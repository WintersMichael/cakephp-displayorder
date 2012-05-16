cakephp-displayorder
====================

A model behavior for CakePHP v2.x .  Released under GPL, though I'll give you a private use license if you ask nicely.

So, you have some things on your website.  Those things should get displayed in a certain order.  Maybe you'd like to control that order.  Enter: DisplayOrder.


Quickstart:
First, create an integer field on your table named 'displayorder' and copy DisplayOrderBehavior.php into app/Model/Behavior/ .

Then, in your Model:

```php
$actsAs = array('DisplayOrder');
```

and, optionally:
```php
$order = 'MyModel.displayorder';
```

(or you can include an 'order' clause on your model queries.)



From your Controller you can now:

```php
$this->MyModel->moveUp();
```

or

```php
$this->MyModel->moveDown();
```

I know, right?  Sweet.

"But web dude," I hear you say, "'displayorder' is stupid, my column is called 'OrderOfThings'!"

Ok, from your model:

```php
$actsAs = array('DisplayOrder' => array(
  'DisplayOrderField' => 'OrderOfThings'
));
```

Fin!  Fun!

"Yeah, but I have relationships in my database.  I need the Up/Down code to understand that it should only be reordering things in a specific Category / Gallery / Other Relationship."

That's cool.  You want to have other relationships, I'm ok with that.  I'll even help you.

Let's say you have Galleries of Images.  Images should only go up/down relative to other Images in that Gallery.

From your ImagesController, do this before any moveUp/moveDown/create/delete on your Model:
```php
$this->Image->displayOrderConditions = array('Image.gallery_id' => $galleryId);
```

You can pass any conditions array that you would pass anywhere else in Cake.

Also, very important!!  If you have your model associations set up so that Images are dependent upon Galleries (ie, deleting Galleries also deletes any Images inside it), you will need to do this from the GalleriesController BEFORE calling Gallery->delete()

```php
$this->Gallery->Image->displayOrderConditions = array('Image.gallery_id' => $galleryId);
```

Otherwise, deleting a Gallery will cause DisplayOrder to reorder ALL of your Images!



Caveats?
====================

-In the name of being useful across as many datasources as possible and ultimately not thoroughly caring enough, this code doesn't use transactions.  If you have a website where things will be reordered by lots of concurrent users, you probably shouldn't use this.

-No ability to moveUp(5) to move an item up 5 entries

-New entries always get put at the bottom.  (max(displayorder) + 1).  There's no option for this, but there probably should be.

-No "insert" function.

In my applications I haven't needed any of this functionality.  If you want to add it, please pass your code along.  Or, you can try to whisper sweet nothings at me and I might decide to do it for kicks.

Also, I think there are probably dozens of implementations like this out there.  I just couldn't find an applicable one after about 20 minutes of searching so decided to write my own.  Maybe I suck at searching.