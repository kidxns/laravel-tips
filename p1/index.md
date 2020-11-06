### Increments and Decrements

##### Instead of this:

```sh
$article = Article::find($article_id);
$article->read_count++;
$article->save();
```

##### You can do this:

```sh
`$article = Article::find($article_id);`
`$article->increment('read_count');`
```

##### Also these will work:

```sh
Article::find($article_id)->increment('read_count');
Article::find($article_id)->increment('read_count', 10); // +10
Product::find($produce_id)->decrement('stock'); // -1
```

### firstOrCreate()

##### Instead of:

```sh
$user = User::where('email', $email)->first();
if (!$user) {
  User::create([
    'email' => $email
  ]);
}
```

##### Do just this:

```sh
$user = User::firstOrCreate(['email' => $email]);
```

### Relationship with conditions and ordering

##### This is a typical way to define relationship:

```sh
public function users() {
    return $this->hasMany('App\User');
}
```

##### But did you know that at this point we can already add where or orderBy?

###### For example, if you want a specific relationship for some type of users, also ordered by email, you can do this:

```sh
public function approvedUsers() {
    return $this->hasMany('App\User')->where('approved', 1)->orderBy('email');
}
```

### Model properties: timestamps, appends etc.

```sh
class User extends Model {
    protected $table = 'users';
    protected $fillable = ['email', 'password']; // which fields can be filled with User::create()
    protected $dates = ['created_at', 'deleted_at']; // which fields will be Carbon-ized
    protected $appends = ['field1', 'field2']; // additional values returned in JSON
    protected $primaryKey = 'uuid'; // it doesn't have to be "id"

    public $incrementing = false; // and it doesn't even have to be auto-incrementing!
    protected $perPage = 25; // Yes, you can override pagination count PER MODEL (default 15)
    const CREATED_AT = 'created_at';
    const UPDATED_AT = 'updated_at'; // Yes, even those names can be overridden
    public $timestamps = false; // or even not used at all

}
```
[more](https://github.com/laravel/framework/blob/5.6/src/Illuminate/Database/Eloquent/Model.php)

### WhereX
##### There’s an elegant way to turn this:

```sh
$users = User::where('approved', 1)->get();

```

##### Into

```sh
$users = User::whereApproved(1)->get(); 
User::whereDay('created_at', date('d'));
User::whereMonth('created_at', date('m'));
User::whereYear('created_at', date('Y'));
```

### Order by relationship

```sh
public function latestPost()
{
    return $this->hasOne(\App\Post::class)->latest();
}
```
###### =>

```sh
$users = Topic::with('latestPost')->get()->sortByDesc('latestPost.created_at');
```

### Eloquent::when() – no more if-else’s
```sh
$query = Author::query();
$query->when(request('filter_by') == 'likes', function ($q) {
    return $q->where('likes', '>', request('likes_amount', 0));
});
$query->when(request('filter_by') == 'date', function ($q) {
    return $q->orderBy('created_at', request('ordering_rule', 'desc'));
});
```

### BelongsTo Default Models

##### Insteads of
```sh
{{ $post->author->name ?? '' }}

```
##### Into
```sh
public function author()
{
    return $this->belongsTo('App\Author')->withDefault([
        'name' => 'Guest Author'
    ]);
}
```

### Raw query methods

```sh
// whereRaw
$orders = DB::table('orders')
    ->whereRaw('price > IF(state = "TX", ?, 100)', [200])
    ->get();

// havingRaw
Product::groupBy('category_id')->havingRaw('COUNT(*) > 1')->get();

// orderByRaw
User::where('created_at', '>', '2016-01-01')
  ->orderByRaw('(updated_at - created_at) desc')
  ->get();  

```

### Replicate: make a copy of a row

```sh

$task = Tasks::find(1);
$newTask = $task->replicate();
$newTask->save();

```

### Chunk() method for big tables

```sh

User::chunk(100, function ($users) {
    foreach ($users as $user) {
        // ...
    }
});

```

### Create additional things when creating a model

```sh
php artisan make:model Company -mc
```

### Transform brackets into an Eloquent query

```sh
$q->where(function ($query) {
    $query->where('gender', 'Male')
        ->where('age', '>=', 18);
})->orWhere(function($query) {
    $query->where('gender', 'Female')
        ->where('age', '>=', 65); 
})
```


### orWhere with multiple parameters

##### Instead of
```sh
$q->where('a', 1);
$q->orWhere('b', 2);
$q->orWhere('c', 3);
```

##### Should do

```sh
$q->where('a', 1);
$q->orWhere(['b' => 2, 'c' => 3]);
```
