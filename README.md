# Vue 3 + Vite




```
Schema::create('followers', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('follower_id');
    $table->unsignedBigInteger('following_id');
    $table->timestamps();

    $table->foreign('follower_id')->references('id')->on('users')->onDelete('cascade');
    $table->foreign('following_id')->references('id')->on('users')->onDelete('cascade');

    $table->unique(['follower_id', 'following_id']);
});
```
