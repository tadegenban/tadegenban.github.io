## Moo introduction
### Perl 轻量级面向对象

<br/>
<br/>
<br/>
tadegenban@gmail.com
             
---

<!-- .slide: class="Moo" font-size="50" --> 
<br/>

    $ perl -Moo

     ______
    < Moo! >
     ------
            \   ^__^
             \  (oo)\_______
                (__)\       )\/\
                    ||----w |
                    ||     ||
            
---

## About Moo

- Created by Matt Trout (MST), first release in 2010
  - DBIx::Class  <!-- .element: class="fragment" data-fragment-index="1" --> 
  - local::lib   <!-- .element: class="fragment" data-fragment-index="2" --> 
  - Catalyst ( co-maintainer )  <!-- .element: class="fragment" data-fragment-index="3" --> 

- 967 Modules on CPAN dependent on Moo  <!-- .element: class="fragment" data-fragment-index="4" --> 

- Moo 2.0  released in MAR 03, 2015  <!-- .element: class="fragment" data-fragment-index="5" --> 

---
## What Moo Looks like

    package Dog;
    use Moo;
    
    has name => is => 'rw';
    has age  => (
        is  => 'rw',
        default => 10,
        );
    
    sub greeting {
        my $self = shift;
        say '汪';
    }

---

### Moose  
### Moos   
### Moo    
### Mo     
### M      
### Mouse  

---

## Moose
The One , The Only, The Original, The Awesome  <!-- .element: class="fragment" data-fragment-index="1" --> 

## Moo  <!-- .element: class="fragment" data-fragment-index="3" --> 
provides almost -- but not quite -- two thirds of Moose  <!-- .element: class="fragment" data-fragment-index="4" --> 

## Mo <!-- .element: class="fragment" data-fragment-index="5" --> 
Mo is less. Much less.   <!-- .element: class="fragment" data-fragment-index="6" --> 

## M <!-- .element: class="fragment" data-fragment-index="8" --> 
M does absolutely nothing  <!-- .element: class="fragment" data-fragment-index="9" --> 

---

## Old School Perl OO

    package Dog;
    
    sub new {
        my $pkg = shift;
        my %opt = @_;
        my $self = \%opt;
        
        bless $self, $pkg;
    }
    
    sub my_method { ... }
    
    Dog->new( name => 'jojo', age => 10 );

---

## why Moo

- bless is simple <!-- .element: class="fragment" data-fragment-index="1" --> 
 - but not handy <!-- .element: class="fragment" data-fragment-index="2" --> 
- Moose is Great <!-- .element: class="fragment" data-fragment-index="3" --> 
 -  but XS dependenies <!-- .element: class="fragment" data-fragment-index="4" --> 
 -  slow start time <!-- .element: class="fragment" data-fragment-index="5" --> 

---

## Moo features

- Moo is pure Perl ( no XS )  <!-- .element: class="fragment" data-fragment-index="1" --> 
- Moo has a fast start time <!-- .element: class="fragment" data-fragment-index="2" --> 
 - (Mo is more fast than Moo) <!-- .element: class="fragment" data-fragment-index="3" --> 
- compatible with Moose <!-- .element: class="fragment" data-fragment-index="4" --> 

---

## Attribute

---

## has
    package Dog;
    
    has name => is => 'rw';
    has birthday => is => 'ro';
    
    my $dog = Dog->new( name => 'jojo',
                        birthday => '2014.11.11');
    $dog->name('coco'); # ok
    $dog->birthday('2014.2.14'); # fail

---

## default and builder

     package Dog;
     
     has master => is => 'rw', default => sub { Master->new() };
     has home   => is => 'rw', default => sub { $_[0]->master->home };
     
     
<br/>
     
     has master => is => 'rw', default => sub { Master->new() };
     has home   => is => 'rw', builder => 1;
     
     sub _build_home { $_[0]->master->home }
     
Note:Because Attribute initialization order is unpredictable . This maybe fail

---

## lazy


     has master => is => 'rw', default => sub { Master->new() };
     has home   => is => 'rw', lazy => 1, builder => 1;  # lazy means create when needed
     
     sub _build_home { $_[0]->master->home }

---

## trigger

    package Dog;
    
    has name => is => 'rw', trigger => 1;
    has name => is => 'rw', trigger => sub { ... };
    
    sub _trigger_name { say 'I have a name -- ' . $_[0]->name }
    
<br/>

    my $dog = Dog->new(name => 'coco'); # print: I have a name -- coco 

---

## handles

     package Config;
     use Moo;
     use JSON::XS ();
     
     has _json => (
       is      => 'ro', 
       handles => [qw/decode encode/],
       default => sxub { JSON::XS->new },
     );

---

## Method

    sub greeting {
        my $self = shift;
        
        ...
    }

---

## extends

    package Dog;
    use Moo;
    extends 'Animal';


---

## Method modifier

---

## before
    package Dog;
    extends 'Animal';
    
    before eat => sub {
        my ( $self, $food ) = @_;
        
        die if $food eq 'grass';
    
    };

- before is called before the method it is modifying <!-- .element: class="fragment" data-fragment-index="1" --> 
- Receives same params as the original method <!-- .element: class="fragment" data-fragment-index="2" --> 

---

## after
    package Dog;
    extends 'Animal';
    
    after eat => sub {
        my ( $self, $food ) = @_;
        
        $self->log("has eat $food");
    
    };

- after is called after the method it is modifying <!-- .element: class="fragment" data-fragment-index="1" --> 
- Receives same params as the original method <!-- .element: class="fragment" data-fragment-index="2" --> 

---

## around
     package Dog;
     extends 'Animal';
     
     around eat => sub {
         my ( $orig, $self, $food ) = @_;
         
         my $ret = $orig->($self, $food);
         do_something($ret);
     
     };

- around is called instead of the method it is modifying <!-- .element: class="fragment" data-fragment-index="1" --> 

- The method you're overriding is passed in as the first argument  <!-- .element: class="fragment" data-fragment-index="2" --> 

---

## Types

---

## build your own sub

    use Scalar::Util qw/looks_like_number/;
    
    has age => (
        is  => 'rw',
        isa => sub { die unless looks_like_number($_[0]) },
    );

---

## MooX::Types::MooseLike

    use MooX::Types::MooseLike::Base qw/:all/;
    
    has age => (is => 'ro', isa => Int);
    
    has beers_by_day_of_week => isa => HashRef;
 
    has current_BAC => isa => Num;
    
    has dog_list => isa => ArrayRef[InstanceOf['Dog']];

---

## Role

- Role is like mixin in Ruby or Java interface <!-- .element: class="fragment" data-fragment-index="1" --> 

- Role can have attributes and methodes <!-- .element: class="fragment" data-fragment-index="2" --> 

- Role can do other roles <!-- .element: class="fragment" data-fragment-index="3" --> 

- Role add behavior or state to a class <!-- .element: class="fragment" data-fragment-index="4" --> 

---

## Role example

    package CanSpeak;
    use Moo::Role;
    
    
    sub speak { say '汪' }

<br/>

    package CanRun;
    use Moo::Role;
    
    
    sub run { say 'running' }

<br/>  
  
    package Dog;
    use Moo;
    with 'CanSpeak';
    with 'CanRun';
    
    
    my $dog = Dog->new();
    $dog->speek();
    $dog->run();
    
---

## requires

    package CanSpeak;
    use Moo::Role;
    
    requires 'words';
    
    sub speak { say $self->words->[int rand 10] }

<br/>

    package Dog;
    use Moo;
    with 'CanSpeak';
    
    has 'words' => (is => 'ro', default => sub { ['汪', '汪汪', ....] }); 
    
    my $dog = Dog->new();
    $dog->speek();

---


## does


```
    print "hi" if $dog->does('CanSpeak');
    
```

---

## Thanks

