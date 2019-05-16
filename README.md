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


