Symfony ORM Test Application
========

A Symfony project created on January 22, 2016.

## Components
This project presents the following components:
1. A default Symfony 3.0 Project
2. Doctrine ORM version 2.5.4
3. Doctrine Migration Bundle.

## Entity
The test application contains a test entity located in `src/AppBundle/Entity` that contains the following basic code used to generate a sample object.

```
namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
* @ORM\Entity
* @ORM\Table(name="product")
*/
class Product
{
   /**
    * @ORM\Column(type="integer")
    * @ORM\Id
    * @ORM\GeneratedValue(strategy="AUTO")
    */
   protected $id;

   /**
    * @ORM\Column(type="string", length=100)
    */
   protected $name;

   /**
    * @ORM\Column(type="decimal", scale=2)
    */
   protected $price;

   /**
    * @ORM\Column(type="text")
    */
   protected $description;
```
The "getters" and "setters" for the object are generated automatically with the following command:
```php bin/console doctrine:generate:entities AppBundle/Entity/Product```
The result is the following functions appended to the Product class:
```
    /**
     * Get id
     *
     * @return integer
     */
    public function getId()
    {
        return $this->id;
    }

    /**
     * Set name
     *
     * @param string $name
     *
     * @return Product
     */
    public function setName($name)
    {
        $this->name = $name;

        return $this;
    }

    /**
     * Get name
     *
     * @return string
     */
    public function getName()
    {
        return $this->name;
    }

    /**
     * Set price
     *
     * @param string $price
     *
     * @return Product
     */
    public function setPrice($price)
    {
        $this->price = $price;

        return $this;
    }

    /**
     * Get price
     *
     * @return string
     */
    public function getPrice()
    {
        return $this->price;
    }

    /**
     * Set description
     *
     * @param string $description
     *
     * @return Product
     */
    public function setDescription($description)
    {
        $this->description = $description;

        return $this;
    }

    /**
     * Get description
     *
     * @return string
     */
    public function getDescription()
    {
        return $this->description;
    }
```
## MySQL Schema creation
At this point the database schema can be created using the following command:
```
php bin/console doctrine:schema:update --force
```
This generates and runs the following SQL:
```
CREATE TABLE `product` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `name` varchar(100) COLLATE utf8_unicode_ci NOT NULL,
  `price` decimal(10,2) NOT NULL,
  `description` longtext COLLATE utf8_unicode_ci NOT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8 COLLATE=utf8_unicode_ci;
```
## Doctrine Migrations
At this point the doctrine migrations bundle takes over the management of schema changes between development and production installations.  The bundle must first be registered in ``//app/AppKernel.php`` as follows:
```
public function registerBundles()
{
    $bundles = array(
        //...
        new Doctrine\Bundle\MigrationsBundle\DoctrineMigrationsBundle(),
    );
}
```
The doctrine migrations must also be set in the ``config.yml`` file as follows:
```
doctrine_migrations:
    dir_name: "%kernel.root_dir%/DoctrineMigrations"
    namespace: Application\Migrations
    table_name: migration_versions
    name: Application Migrations
```
