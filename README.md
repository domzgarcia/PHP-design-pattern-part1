### laravel-design-pattern
Quick study of design patterns used in Laravel

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

Builder Pattern
---
Builds complex obj step by step, it can return different obj based on the given data. Extension of Factory pattern.
- Decouples code.
- Focuses on building complex obj and return them.
- Has functionality to decide which obj should be returned.

```php
interface PizzaBuilderInterface // adhere to this contract
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

```



