Wordpress Corcel
================
*This is a fork of [jgrossi/corcel](https://github.com/jgrossi/corcel)*

--

Corcel is a class collection created to retrieve Wordpress database data using a better syntax. It uses the Eloquent ORM developed for the Laravel 5 Framework.

This way you can use Wordpress as back-end, to insert posts, custom types, etc.

## Installation

To install Corcel just create a `composer.json` file and add:

    "require": {
        "redbeed/corcel-laravel5": "dev-master"
    },

After that run `composer update` and wait.

## Usage

First you must add the service provider to to `config/app.php` under `providers`

    Corcel\CorcelServiceProvider::class

Now you must publish the assets:

    php artisan vendor:publish

You can specify the connection in the corcel config file.

    config/corcel.php



### Posts

    // All published posts
    $posts = Post::published()->get();
    $posts = Post::status('publish')->get();

    // A specific post
    $post = Post::find(31);
    echo $post->post_title;

You can retrieve meta data from posts too.

    // Get a custom meta value (like 'link' or whatever) from a post (any type)
    $post = Post::find(31);
    echo $post->meta->link; // OR
    echo $post->fields->link;
    echo $post->link; // OR

Updating post custom fields:

    $post = Post::find(1);
    $post->meta->username = 'juniorgrossi';
    $post->meta->url = 'http://grossi.io';
    $post->save();

Inserting custom fields:

    $post = new Post;
    $post->save();

    $post->meta->username = 'juniorgrossi';
    $post->meta->url = 'http://grossi.io';
    $post->save();

### Custom Post Type

You can work with custom post types too. You can use the `type(string)` method or create your own class.

    // using type() method
    $videos = Post::type('video')->status('publish')->get();

    // using your own class
    class Video extends Corcel\Post
    {
        protected $postType = 'video';
    }
    $videos = Video::status('publish')->get();

Custom post types and meta data:

    // Get 3 posts with custom post type (store) and show its title
    $stores = Post::type('store')->status('publish')->take(3)->get();
    foreach ($stores as $store) {
        $storeAddress = $store->address; // option 1
        $storeAddress = $store->meta->address; // option 2
        $storeAddress = $store->fields->address; // option 3
    }

### Taxonomies

You can get taxonomies for a specific post like:

    $post = Post::find(1);
    $taxonomy = $post->taxonomies()->first();
    echo $taxonomy->taxonomy;

Or you can search for posts using its taxonomies:

    $post = Post::taxonomy('category', 'php')->first();

### Pages

Pages are like custom post types. You can use `Post::type('page')` or the `Page` class.

    // Find a page by slug
    $page = Page::slug('about')->first(); // OR
    $page = Post::type('page')->slug('about')->first();
    echo $page->post_title;

### Categories & Taxonomies

Get a category or taxonomy or load posts from a certain category. There are multiple ways
to achief it.

    // all categories
    $cat = Taxonomy::category()->slug('uncategorized')->posts()->first();
    echo "<pre>"; print_r($cat->name); echo "</pre>";

    // only all categories and posts connected with it
    $cat = Taxonomy::where('taxonomy', 'category')->with('posts')->get();
    $cat->each(function($category) {
        echo $category->name;
    });

    // clean and simple all posts from a category
    $cat = Category::slug('uncategorized')->posts()->first();
    $cat->posts->each(function($post) {
        echo $post->post_title;
    });


### Attachment and Revision

Getting the attachment and/or revision from a `Post` or `Page`.

    $page = Page::slug('about')->with('attachment')->first();
    // get feature image from page or post
    print_r($page->attachment);

    $post = Post::slug('test')->with('revision')->first();
    // get all revisions from a post or page
    print_r($post->revision);

## Licence

Corcel is licensed under the MIT license.
