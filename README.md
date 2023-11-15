# Vue 3 + Vite




```
Schema::create('followers', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('user_id');
    $table->unsignedBigInteger('follower_id');
    $table->timestamps();

    $table->foreign('user_id')->references('id')->on('users')->onDelete('cascade');
    $table->foreign('follower_id')->references('id')->on('users')->onDelete('cascade');
});
```

### Relationship 
```
 public function followers()
{
    return $this->belongsToMany(User::class, 'followers', 'user_id', 'follower_id');
}

public function following()
{
    return $this->belongsToMany(User::class, 'followers', 'follower_id', 'user_id');
}
```

### Route
```
Route::post('/follow/{user}', [FollowerController::class, 'follow'])->name('follow');
Route::delete('/unfollow/{user}', [FollowerController::class, 'unfollow'])->name('unfollow');
```

### FollowerController
```

class FollowerController extends Controller
{
    public function follow(User $user)
    {
        // Get the currently authenticated user
        $follower = auth()->user();

        // Check if the user is not trying to follow themselves
        if ($follower->id == $user->id) {
            return redirect()->back()->with('error', 'You cannot follow yourself.');
        }

        // Check if the follower is already following the user
        if ($follower->following()->where('follower_id', $user->id)->exists()) {
            return redirect()->back()->with('error', 'You are already following this user.');
        }

        // Add the follower
        $follower->following()->attach($user->id);

        return redirect()->back()->with('success', 'You are now following ' . $user->name);
    }

    public function unfollow(User $user)
    {
        auth()->user()->following()->detach($user->id);
        return back();
    }
}
```
