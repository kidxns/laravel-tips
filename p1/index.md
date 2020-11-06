## Increments and Decrements

### Instead of this:

* `$article = Article::find($article_id);`
* `$article->read_count++;`
* `$article->save();`

### You can do this:

* `$article = Article::find($article_id);`
* `$article->increment('read_count');`

### Also these will work:
* `Article::find($article_id)->increment('read_count');`
* `Article::find($article_id)->increment('read_count', 10); // +10`
* `Product::find($produce_id)->decrement('stock'); // -1`


