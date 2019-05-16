### laravel-design-pattern
Quick study of design patterns used in Laravel


# Section 1 - By Bobby Bouwmann.

Factory Pattern
---
- provides an interface for creating obj with specifying their concrete classes.
- decouples code
- Factory is responsible for creating obj, not the client.
- Multiple clients call the same factory, one place for changes
- Easier to test, easy to mock and isolate.

```php
interface PizzaFactoryContract
{
  public function make(array $toppings = []): Pizza;
}
class PizzaFactory implements PizzaFactoryContract
{
  public function make(array $toppings=[])
  {
    return new Pizza($toppings);
  }
}
// # assemble pizza together

```
In laravel
```php
function view($view = null, $data = [], $mergeData = [])
{
  $factory = app(ViewFactory::class);
  if( func_num_args() === 0) {
    return $factory;
  }
  return $factory->make($view, $data, $mergeData);
}
```

Builder Pattern (Manager Pattern)
---
Builds complex obj step by step, it can return different obj based on the given data. Extension of Factory pattern.
- Decouples code.
- Focuses on building complex obj and return them.
- Has functionality to decide which obj should be returned.

```php
interface PizzaBuilderInterface // # adhere to this contract
{
  public function prepare();
  public function applyToppings();
  public function bake();
}
class PizzaBuilder
{
  public function make(PizzaBuilderInterface $pizza): Pizza 
  {
    $pizza->prepare();
    $pizza->applyToppings();
    $pizza->bake();
    return $pizza;
  }
}
// # So building a Margarita Pizza
class MargarithaBuilder implements PizzaBuilderInterface
{
  protected $pizza;
  public function prepare(): Pizza
  {
    $this->pizza = new Pizza();
    
    return $this->pizza();
  }
  public function applyToppings(): Pizza
  {
    $this->pizza->setToppings(['cheese','tomato']);
    
    return $this->pizza();
  }
  public function bake(): Pizza
  {
    $this->pizza->setBakingTemperature(180);
    $this->pizza->setBakingMinutes(10);
    
    return $this->pizza();
  }
}

// # Create a PizzaBuilder
$pizzaBuilder = new PizzaBuilder;

// # Create pizza using Builder which returns Pizza instance
$pizza = $pizzaBuilder->make(new MargarithaBuilder());

// # adding new Builder
class ChickenBuilder implements PizzaBuilderInterface 
{
  protected $pizza;
  
  public funciton prepare(): Pizza
  {
    $this->pizza = new Pizza();
    
    return $this->pizza;
  }
  public funciton applyToppings(): Pizza
  {
    $this->pizza->setToppings(['cheese','tomato','chicken','corn']);
    
    return $this->pizza;
  }
  public funciton bake(): Pizza
  {
    $this->pizza->setBakingTemperature(200);
    $this->pizza->setBakingMinutes(10);
    
    return $this->pizza;
  }
}

// # Building pizzas with our first example implemented as follow.
$pizzaBuilder = new PizzaBuilder;

$pizzaOne = $pizzaBuilder->make(new MargarithaBuilder());
$pizzaTwo = $pizzaBuilder->make(new ChickenBuilder());

// # URL 
```
[https://www.youtube.com/watch?v=mNl4cMLlplM](visit)

Strategy Pattern
---
Defines a family of algorithms that are interchangeable. (if no bike, scooter, or any). Behavioral Pattern.
Program to an interface, not an implemation. OPEN and CLOSE principle.

```php
interface DeliveryStrategy
{ 
  public function deliver(Address $address): DeliveryTime;
}

// # Calculate delivery time.
class BikeDelivery implements DeliveryStrategy
{
  public function delivery(Address $address): DeliveryTime
  {
    $route = new BikeRoute($address);
    echo $route->calculateCosts();
    echo $route->calculateDeliveryTime();
  }
}
// # So deliver the pizza now 
class PizzaDelivery
{ 
  public function deliverPizza(DeliveryStrategy $strategy, Address $address)
  {
    return $strategy->deliver($address);
  }
}
$address = new Address('Meer en vaart 300 1058 LE Amsterdam');
$delivery = new PizzaDelivery();

$delivery->deliver(new BikeDelivery(), $address);

// # So adding new type of delivery
class DroneDelivery implements DeliveryStrategy
{
  public function delivery(Address $address): DeliveryTime
  {
    $route = new DroneRoute($address);
    echo $route->calculateCosts();
    echo $route->calculateFlyTime();
  }
}

// Multiple ways of sending pizza
class PizzaDelivery
{
  public function deliverPizza(DeliveryStrategy $strategy, Address $address)
  {
    return $strategy->deliver($address);
  }
}

$address = new Address('Meer en vaart 300 1058 LE Amsterdam');
$delivery = new PizzaDelivery();

$delivery->deliver(new BikeDelivery(), $address);
$delivery->deliver(new DroneDelivery(), $address);

// # In laravel strategy pattern used in Translation Locale.
```
Provider Pattern
---
Sets a pattern for providing some essential service
Also interchangeable.

```php

// # Like how do it.

class DominoServiceProvider extends ServiceProvider
{
  public function register()
  {
    // register your services here
  }
}

use App\Dominos\PizzaManager;

class PizzaServiceProvider extends ServiceProvider
{
  public function register()
  {
    $this->app->bind('pizza-manager', function ($app) {
      return new PizzaManager();
    });
  }
}

// # Usage
$pizzaManager = new \App\Dominos\PizzaManager();

// Using container
$pizzaManager = app('pizza-manager');

$margaritha = $pizzaManager->driver('margaritha');
$chicken = $pizzaManager->driver('chicken-pizza');

// # Example in Laravel Provider Pattern

class DebugServiceProvider extends ServiceProvider 
{
  public function register()
  {
    $this->app->singleton('debugbar', function ($app) {
      $debugbar = new LaravelDebugbar($app);
      
      if($app->bound(SessionManager::class)) {
        $sessionManager = $app->make(SessionManager::class);
        $httpDriver = new SymfonyHttpDriver($sessionManager);
        $debugbar->setHttpDriver($httpDriver);
      } 
      return $debugbar;
    });
  }
}
```


# Section 2 - By Jeffrey Way
Things Laravel Made me Believe
[https://www.youtube.com/watch?v=mDotS5BDqRM](visit)

Recurring themes:
- Care
- Simplicity
- Distrust

### Things I Believe
- What's Unimportant is Paramount ~ `The things we think don't matter, maybe matter the most`
- Simple(r) Always Wins
- API over Purity
- Be Honest About Scope
- Focus on the Fundamentals (Loud and clear)
- Just Build Things

### Writing Software is Hard
#### Really Hard






