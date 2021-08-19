---
title: 'CRUD Contact Secara realtime'
date: 2021-08-15T09:03:11+07:00
UrlGambar: 'https://i.postimg.cc/hcGP2QhF/Screenshot-from-2021-08-15-08-51-29.png'
previewlink: 'https://github.com/IzzaWildanRidhoni/livewire/edit/master/README.md'
tools:
  - laravel
  - bootstrap
  - html
  - css
  - js
---

# Laravel Livewire

diprogram ini dibuat menggunakan laravel dan livewire dengan beberapa fitur

- Create Contact realtime
- Edit Contact realtime
- Delete Contact realtime
- Mengatur Data Pagination secara realtime
- Mencari Data secara realtime

untuk mencobanya anda bisa clone repositori ini dengan cara

```
git clone https://github.com/IzzaWildanRidhoni/livewire.git

```

> nb: pastikan anda telah menginstal git

berikut **preview** dari program yang saya buat
![gambar laravel livewire](https://i.postimg.cc/hcGP2QhF/Screenshot-from-2021-08-15-08-51-29.png)

---

# Dokumentasi belajar Laravel Livewire

yang pertama dilakukan adalah membuat projek laravel dengan cara

## 1. Membuat Projek

membuat projek laravel kita dengan nama `livewire`

```php
composer create-project --prefer-dist laravel/laravel:^7.0 livewire
```

## 2. menambahkan authentikasi

jalankan perintah

```bash
composer require laravel/ui
```

menggunakan bootstrap

```
php artisan ui bootstrap --auth
```

setelah itu lakukan

```bash
npm run dev
```

## 3. membuat Model dan Migration Contact

untuk membuat model berserta migration Contact maka lakukan perintah berikut ini

```
php artisan make:model Contact -m
```

maka akan dibuatkan model Contact dan migration `create_contacts_table`
.

- #### **ubah migration contact** dengan kode berikut

  ```php
  public function up()
  {
      Schema::create('contacts', function (Blueprint $table) {
          $table->id();
          $table->string('name');
          $table->string('phone',15);
          $table->timestamps();
      });
  }
  ```

- #### **edit model Contact**

  ubah model contact menjadi seperti ini

  ```php
  class Contact extends Model
  {
      protected $guarded = [];
  }
  ```

  lakukan migration

  ```
  php artisan migrate
  ```

## 4. Membuat faker dan seeder

jalankan perintah berikut untuk membuat factory

```
php artisan make:factory ContactFactory
```

dan lokasi factorynya ada di `database/ContactFactory`

- ubah file ContactFactory menjadi

  ```php
  $factory->define(Contact::class, function (Faker $faker) {
    return [
        'name' =>$faker->name,
        'phone' =>$faker->e164PhoneNumber
    ];
  });
  ```

untuk membuat seeder jalankan perintah

```bash
php artisan make:seed ContactTableSeeder
```

maka akan dibuatkan seeder yang berada di folder `database/seeds`

- ubah file ContactTableSeeder
  ```php
    public function run()
    {
        factory(Contact::class,10)->create();
    }
  ```
  jalanlupa tambahkan `use App\Contact;`
- ubah `DatabaseSeeder`
  ```php
   public function run()
    {
        // $this->call(UserSeeder::class);
        $this->call(ContactTableSeeder::class);
    }
  ```

kemudian lakukan migration beserta seedernya

```
php artisan migrate --seed
```

## 5. Menambahkan livewire

lakukan perintah berikut

```
composer require livewire/livewire
```

- setup livewire
  tambahkan `@LivewireStyles` dan `@livewireScripts`
  di dalam file `resource/layout/app.blade.php` maka akan menjadi seperti ini

  ````html
  <!DOCTYPE html>

  <html lang="{{ str_replace('_', '-', app()->getLocale()) }}">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, initial-scale=1" />

      <!-- CSRF Token -->
      <meta name="csrf-token" content="{{ csrf_token() }}" />

      <title>{{ config('app.name', 'Laravel') }}</title>

      <!-- Scripts -->
      <script src="{{ asset('js/app.js') }}" defer></script>

      <!-- Fonts -->
      <link rel="dns-prefetch" href="//fonts.gstatic.com" />
      <link href="https://fonts.googleapis.com/css?family=Nunito" rel="stylesheet" />

      <!-- Styles -->
      <link href="{{ asset('css/app.css') }}" rel="stylesheet" />
      @livewireStyles
    </head>
    <body>
      <div id="app">
        <nav class="navbar navbar-expand-md navbar-light bg-white shadow-sm">
          <div class="container">
            <a class="navbar-brand" href="{{ url('/') }}"> {{ config('app.name', 'Laravel') }} </a>
            <button class="navbar-toggler" type="button" data-toggle="collapse" data-target="#navbarSupportedContent" aria-controls="navbarSupportedContent" aria-expanded="false" aria-label="{{ __('Toggle navigation') }}">
              <span class="navbar-toggler-icon"></span>
            </button>

            <div class="collapse navbar-collapse" id="navbarSupportedContent">
              <!-- Left Side Of Navbar -->
              <ul class="navbar-nav mr-auto"></ul>

              <!-- Right Side Of Navbar -->
              <ul class="navbar-nav ml-auto">
                <!-- Authentication Links -->
                @guest
                <li class="nav-item">
                  <a class="nav-link" href="{{ route('login') }}">{{ __('Login') }}</a>
                </li>
                @if (Route::has('register'))
                <li class="nav-item">
                  <a class="nav-link" href="{{ route('register') }}">{{ __('Register') }}</a>
                </li>
                @endif @else
                <li class="nav-item dropdown">
                  <a id="navbarDropdown" class="nav-link dropdown-toggle" href="#" role="button" data-toggle="dropdown" aria-haspopup="true" aria-expanded="false" v-pre> {{ Auth::user()->name }} </a>

                  <div class="dropdown-menu dropdown-menu-right" aria-labelledby="navbarDropdown">
                    <a
                      class="dropdown-item"
                      href="{{ route('logout') }}"
                      onclick="event.preventDefault();
                                                       document.getElementById('logout-form').submit();"
                    >
                      {{ __('Logout') }}
                    </a>

                    <form id="logout-form" action="{{ route('logout') }}" method="POST" class="d-none">@csrf</form>
                  </div>
                </li>
                @endguest
              </ul>
            </div>
          </div>
        </nav>

        <main class="py-4">@yield('content')</main>
      </div>
      @livewireScripts
    </body>
  </html>

  ```
  ````

---

## **6. membuat Component index**

kemudian langkah selanjutnya setelah setup livewire maka jalankan perintah berikut ini

```
php artisan livewire:make ContactIndex
```

maka akan dibuatkan file livewire di directory di `app/http/livewire/ContactIndex.php` kemudian ubah filenya menjadi seperti ini

```php
<?php

namespace App\Http\Livewire;

use App\Contact;
use Livewire\Component;
use Livewire\WithPagination;

class ContactIndex extends Component
{
    use WithPagination;

    // public $data;
    public $statusUpdate = false;
    public $paginate = 5;
    public $search;

    // emit dihandle oleh handleStored
    protected $listeners = [
        'contactStored' => 'handleStored',
        'contactUpdated' => 'handleUpdated'
    ];

    protected $queryString= ['search'];

    public function mount()
    {
        $this->search = request()->query('search' , $this->search );
    }

    public function render()
    {
        return view('livewire.contact-index',[
            //  'contacts' => Contact::latest()->get()

            //  'contacts' => Contact::latest()->paginate($this->paginate)

            'contacts' =>$this->search === null ?
            Contact::latest()->paginate($this->paginate) :
            Contact::latest()->where('name','like','%'.$this->search.'%')->paginate($this->paginate)
        ]);
    }

    public function getContact($id)
    {
        $this->statusUpdate = true;
        $contact = Contact::find($id);
        $this->emit('getContact',$contact);
    }

    public function destroy($id)
    {
        if ($id) {
            $data = Contact::find($id);
            $data->delete();
            session()->flash('pesan','Contact  was deleted !');
        }
    }

    public function handleStored($contact)
    {
        session()->flash('pesan','Contact '. $contact['name'] .' was stored !');
    }

    public function handleUpdated($contact)
    {
        session()->flash('pesan','Contact '. $contact['name'] .' was update !');
        $this->statusUpdate = false;
    }
}

```

dan ubah juga contact index yang berada di directori `resources/livewire/contact-index.blade.php` menjadi seperti ini

```php
<div>

    @if(session()->has('pesan'))
        <div class="alert alert-success">
            {{ session('pesan') }}
        </div>
    @endif

    @if ($statusUpdate)
    <livewire:contact-update></livewire:contact-update>
    @else
    <livewire:contact-create></livewire:contact-create>
    @endif

    <hr>

    <div class="row">
        <div class="col">
            <select wire:model="paginate" name="" id="" class="form-control-form-control sm w-auto">
                <option value="5">5</option>
                <option value="10">10</option>
                <option value="15">15</option>
            </select>
        </div>
        <div class="col">
            <input wire:model="search" type="text" class="form-control form-control-sm" placeholder="Search">
        </div>
    </div>
    {{-- {{$paginate}} --}}
    <hr>

   <table class="table">
       <thead class="thead-dark">
           <tr>
               <th scope="col">#</th>
               <th scope="col">Name</th>
               <th scope="col">Phone</th>
               <th scope="col"></th>
           </tr>
       </thead>
       <tbody>
           <?php $no =1; ?>
           @foreach ($contacts as $contact)
           <tr>
               <th scope="row">{{$no++}}</th>
               <td>{{$contact->name}}</td>
               <td>{{$contact->phone}}</td>
               <td>
                   <button wire:click="getContact({{$contact->id}})" class="btn btn-sm btn-info text-white">Edit</button>
                   <button wire:click="destroy({{$contact->id}})" class="btn btn-sm btn-danger text-white">Delete</button>
                </td>
            </tr>
            @endforeach
       </tbody>
   </table>
   {{$contacts->links()}}
   {{-- {{$contacts->links('pagination::bootstrap-4')}} --}}
</div>

```

## **7. buat component Contact Update**

dengan cara

```
php artisan livewire:make ContactUpdate
```

setelah itu ubah `ContactUpdate.php` yang ada di dir livewire menjadi seperti ini

```php
<?php

namespace App\Http\Livewire;

use App\Contact;
use Livewire\Component;

class ContactUpdate extends Component
{
    public $name;
    public $phone;
    public $contactId;


    protected $listeners =[
        'getContact' => 'showContact'
    ];

    public function render()
    {
        return view('livewire.contact-update');
    }

    public function showContact($contact)
    {
        $this->name = $contact['name'];
        $this->phone = $contact['phone'];
        $this->contactId = $contact['id'];
    }

    public function update()
    {
         $this->validate([
            'name' => 'required|min:3',
            'phone' => 'required|max:15',
        ]);

        if($this->contactId){
            $contact = Contact::find($this->contactId);
            $contact->update([
                'name' =>$this->name,
                'phone' =>$this->phone,
            ]);

            $this->resetInput();

            $this->emit('contactUpdated',$contact);
        }
    }
    public function resetInput()
    {
        $this->name = null;
        $this->phone = null;
    }
}

```

ubah juga file update yang berada di `resources/livewire/contact_update.blade.php` menjadi seperti ini

```html
<div>
  <form wire:submit.prevent="update">
    <input type="hidden" name="" wire:model="contactId" />
    <div class="form-group">
      <div class="form-row">
        <div class="col">
          <input wire:model="name" type="text" class="form-control @error('name') is-invalid @enderror" placeholder="name" />
          @error('name')
          <span class="invalid-feedback">
            <strong>{{$message}}</strong>
          </span>
          @enderror
        </div>
        <div class="col">
          <input wire:model="phone" type="text" class="form-control @error('phone') is-invalid @enderror" placeholder="phone" />
          @error('phone')
          <span class="invalid-feedback">
            <strong>{{$message}}</strong>
          </span>
          @enderror
        </div>
      </div>
    </div>
    <button type="submit" class="btn btn-sm btn-primary">Update</button>
  </form>
</div>
```

## **8. buat component Create Contact**

buat createCOntact.php

```
php artisan make:livewire ContactCreate
```

maka akan dibuatkan file yang berada di dir `app/http/livewire`. dan ubah isi filenya menjadi seperti ini

```php
<?php

namespace App\Http\Livewire;

use App\Contact;
use Livewire\Component;

class ContactCreate extends Component
{
    public $name;
    public $phone;

    public function render()
    {
        return view('livewire.contact-create');
    }

    public function store()
    {
        $this->validate([
            'name' => 'required|min:3',
            'phone' => 'required|max:15',
        ]);

        $contact = Contact::create([
            'name' => $this->name,
            'phone' => $this->phone
        ]);

        $this->resetInput();

        // ketika sesudah menambahkan data kirim emit
        $this->emit('contactStored',$contact);
    }

    private function resetInput()
    {
        $this->name = null;
        $this->phone = null;
    }
}

```

dan ubah juga file `create_contact.blade.php` yang berada di dir `resources/livewire/create_contact.blade.php` menjadi seperti ini

```html
<div>
  <form wire:submit.prevent="store">
    <div class="form-group">
      <div class="form-row">
        <div class="col">
          <input wire:model="name" type="text" class="form-control @error('name') is-invalid @enderror" placeholder="name" />
          @error('name')
          <span class="invalid-feedback">
            <strong>{{$message}}</strong>
          </span>
          @enderror
        </div>
        <div class="col">
          <input wire:model="phone" type="text" class="form-control @error('phone') is-invalid @enderror" placeholder="phone" />
          @error('phone')
          <span class="invalid-feedback">
            <strong>{{$message}}</strong>
          </span>
          @enderror
        </div>
      </div>
    </div>
    <button type="submit" class="btn btn-sm btn-primary">Tambah</button>
  </form>
</div>
```

setelah selesai ubah route `web.php` menjadi seperti ini

```php
Route::get('/', function () {
    return view('welcome');
});

Auth::routes();

Route::get('/home', 'HomeController@index')->name('home');

```

---

# finish

jalankan perintah berikutf

```
php artisan serve
```
