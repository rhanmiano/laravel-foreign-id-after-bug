## Environment

OS: Pop!_OS 22.04 LTS x86_64

PHP: 8.0.2

Laravel: 9.19

Sail: 1.0.1

## Problem

Was initially encountered this weird behaviour in a personal project that is using Laravel 8+Lighthouse PHP on a Sail (Docker environment). Where in when I tried adding another column (a foreign key) to a table with a chained `->after('existing_column')`, something similar to this, it won't add after the specified column.

```
$table->foreignId('foreign_id')->nullable()->default(null)->constrained('foreign_table')->after('id');
```

### Expected behavior
`foreign_id` should be added after the `id` column.

### Actual behavior
`foreign_id` was added as the last column (at the end of the table).

## Replication steps

Since Laravel 8 has ended its support, I tried replicating it on Laravel 9. It seems that it can be replicated to the current version. As of time of writing, the current version is `9.19`.

- Install the app as instructed on [**Getting Started On Linux**](https://laravel.com/docs/8.x/installation#getting-started-on-linux)
- Run the app using sail `sail up -d`
- Create `profiles` table. For this example I just added initial columns such as 

```
Schema::create('profiles', function (Blueprint $table) {
    $table->id();
    $table->string('first_name');
    $table->string('last_name');
    $table->timestamps();
});
```
- Create another migration for adding a foreign id `user_id` to `profiles` table, also chain it with either `->after('id')` or `->after('first_name')`. 
```
Schema::table('profiles', function (Blueprint $table) {
    $table->foreignId('user_id')->nullable()->default(null)->constrained('users')->after('id');
}); 
```
- Run the migration `sail artisan migrate`

## Other notes

Tried omitting also the `->nullable()->default(null)`, but with no seemingly effect.
