# Inject Symfony Services Into Test Class Properties

A trait to use in your Symfony tests to populate test class properties with their services from the container.

## How to Use It

 - `use TestClassServicePropertyInjectorTrait` in any test that extends `Symfony\Bundle\FrameworkBundle\Test\KernelTestCase`
 - call `$this->injectContainerServicesIntoClassProperties()` in your `setUp()` method
 
## What It Does

Sets all private and protected properties to their respective services. For a property to be set it must:

 - be `private` or `protected`
 - be type-hinted to a non-scalar type
 - have a type hint that matches a service in the container

## Usage Example

```php
use App\Services\FooService;
use App\Services\BarService;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;
use webignition\SymfonyTestServiceInjectorTrait\TestClassServicePropertyInjectorTrait;

class AcmeControllerTest extends WebTestCase
{
    use TestClassServicePropertyInjectorTrait;

    private FooService $fooService;
    private BarService $barService;
    
    protected function setUp(): void
    {
        parent::setUp();
        $this->injectContainerServicesIntoClassProperties();
    }
    
    public function testUsingFooService(): void
    {
        $foo = $this->fooService->createFoo();

        // ... carry out tests that depend on FooService creating $foo
    }
    
    public function testUsingBarService(): void
    {
        $bar = $this->barService->createBar();

        // ... carry out tests that depend on BarService creating $bar
    }
}
```

## Before-and-after Comparison

### Before

```php
use App\Services\ServiceA;
use App\Services\ServiceB;
use App\Services\ServiceC;
use App\Services\ServiceD;
use App\Services\ServiceE;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;

class AcmeControllerTest extends WebTestCase
{
    private ServiceA $serviceA;
    private ServiceB $serviceB;
    private ServiceC $serviceC;
    private ServiceD $serviceD;
    private ServiceE $serviceE;
    
    protected function setUp(): void
    {
        parent::setUp();
        
        $serviceA = self::$container->get(ServiceA::class);
        if ($serviceA instanceof ServiceA) {
            $this->serviceA = $serviceA;
        }

        $serviceB = self::$container->get(ServiceB::class);
        if ($serviceB instanceof ServiceB) {
            $this->$serviceB = $serviceB;
        }

        $serviceC = self::$container->get(ServiceC::class);
        if ($serviceC instanceof ServiceC) {
            $this->$serviceC = $serviceC;
        }

        $serviceD = self::$container->get(ServiceD::class);
        if ($serviceD instanceof ServiceD) {
            $this->$serviceD = $serviceD;
        }

        $serviceE = self::$container->get(ServiceE::class);
        if ($serviceE instanceof ServiceE) {
            $this->$serviceE = $serviceE;
        }
    }
}
```

### After

```php
use App\Services\ServiceA;
use App\Services\ServiceB;
use App\Services\ServiceC;
use App\Services\ServiceD;
use App\Services\ServiceE;
use Symfony\Bundle\FrameworkBundle\Test\WebTestCase;
use webignition\SymfonyTestServiceInjectorTrait\TestClassServicePropertyInjectorTrait;

class AcmeControllerTest extends WebTestCase
{
    use TestClassServicePropertyInjectorTrait;

    private ServiceA $serviceA;
    private ServiceB $serviceB;
    private ServiceC $serviceC;
    private ServiceD $serviceD;
    private ServiceE $serviceE;
    
    protected function setUp(): void
    {
        parent::setUp();
        $this->injectContainerServicesIntoClassProperties();
    }
}
```