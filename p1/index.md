### Increments and Decrements

#### Instead of this:
```sh
$article = Article::find($article_id);
$article->read_count++;
$article->save();
```

#### You can do this:
```sh
`$article = Article::find($article_id);`
`$article->increment('read_count');`
```

#### Also these will work:
```sh
Article::find($article_id)->increment('read_count');
Article::find($article_id)->increment('read_count', 10); // +10
Product::find($produce_id)->decrement('stock'); // -1
```

### firstOrCreate()

#### Instead of:
```sh
$user = User::where('email', $email)->first();
if (!$user) {
  User::create([
    'email' => $email
  ]);
}
```

#### Do just this:
```sh
$user = User::firstOrCreate(['email' => $email]);
```

### Relationship with conditions and ordering

#### This is a typical way to define relationship:

```sh
public function users() {
    return $this->hasMany('App\User');    
}
```

#### But did you know that at this point we can already add where or orderBy?
###### For example, if you want a specific relationship for some type of users, also ordered by email, you can do this:

```sh
public function approvedUsers() {
    return $this->hasMany('App\User')->where('approved', 1)->orderBy('email');
}
```



