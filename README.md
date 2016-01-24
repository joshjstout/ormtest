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
Application Migrations now looks like this:
```
root@metrics-test:/var/www/html# php bin/console doctrine:migrations:status

 == Configuration

    >> Name:                                               Application Migrations
    >> Database Driver:                                    pdo_mysql
    >> Database Name:                                      ormtest
    >> Configuration Source:                               manually configured
    >> Version Table Name:                                 migration_versions
    >> Version Column Name:                                version
    >> Migrations Namespace:                               Application\Migrations
    >> Migrations Directory:                               /var/www/orm_test/app/DoctrineMigrations
    >> Previous Version:                                   Already at first version
    >> Current Version:                                    0
    >> Next Version:                                       Already at latest version
    >> Latest Version:                                     0
    >> Executed Migrations:                                0
    >> Executed Unavailable Migrations:                    0
    >> Available Migrations:                               0
    >> New Migrations:                                     0

```
and a ``migration_versions`` table now exists in our database.

Next we add a User entity to our model and run ```php bin/console doctrine:generate:entities AppBundle/Entity/User``` to generate our get/set functions.
```
<?php
namespace AppBundle\Entity;

use Doctrine\ORM\Mapping as ORM;

/**
 * @ORM\Entity
 * @ORM\Table(name="hello_user")
 */
class User
{
    /**
     * @ORM\Id
     * @ORM\Column(type="integer")
     * @ORM\GeneratedValue(strategy="AUTO")
     */
    protected $id;

    /**
     * @ORM\Column(type="string", length=255)
     */
    protected $name;

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
     * @return User
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
}
```
Again, we generate the schema with ```php bin/console doctrine:schema:update --force```

We can generate a migration class to capture the database state with
```
php bin/console doctrine:migrations:generate
Generated new migration class to "/var/www/orm_test/app/DoctrineMigrations/Version20160122223908.php"
```

Of course, the end goal of writing migrations is to be able to use them to reliably update the database structure when the application is deployed. By running the migrations locally (or on a beta server), you can ensure that the migrations work as expected.

The doctrine:migrations:migrate command handles the migration. Doctrine creates a migration_versions
 table to tracks which migrations have been executed.  The table currently looks like this:

|Version|
|:-------------|
|20160122223908|
|20160122224707|

So, no matter how many migrations are created and executed locally, when you run the command during deployment, Doctrine will know exactly which migrations have not been run yet by looking at the migration_versions table of your production database. Regardless of what server you’re on, you can always safely run this command to execute only the migrations that haven’t been run yet on that particular database.

Let's add a field to User:
```
    /**
     * @ORM\Column(type="boolean")
     */
    protected $active;
```

After making changes to the entity model it is important to run:
```
root@metrics-test:/var/www/html# php bin/console doctrine:migrations:diff
Generated new migration class to "/var/www/orm_test/app/DoctrineMigrations/Version20160122230602.php" from schema differences.
```

Status now has a version:
```
root@metrics-test:/var/www/html# php bin/console doctrine:migrations:status

 == Configuration

    >> Name:                                               Application Migrations
    >> Database Driver:                                    pdo_mysql
    >> Database Name:                                      ormtest
    >> Configuration Source:                               manually configured
    >> Version Table Name:                                 migration_versions
    >> Version Column Name:                                version
    >> Migrations Namespace:                               Application\Migrations
    >> Migrations Directory:                               /var/www/orm_test/app/DoctrineMigrations
    >> Previous Version:                                   2016-01-22 22:39:08 (20160122223908)
    >> Current Version:                                    2016-01-22 22:47:07 (20160122224707)
    >> Next Version:                                       2016-01-22 23:06:02 (20160122230602)
    >> Latest Version:                                     2016-01-22 23:06:02 (20160122230602)
    >> Executed Migrations:                                2
    >> Executed Unavailable Migrations:                    0
    >> Available Migrations:                               3
    >> New Migrations:                                     1

```

At this point the database schema does not have the modifications in it.  We could run:
```
php bin/console doctrine:schema:update --force
```
But this would go around the migration system.  So instead it is important to run:
```
root@metrics-test:/var/www/html# php bin/console doctrine:migrations:migrate

                    Application Migrations


WARNING! You are about to execute a database migration that could result in schema changes and data lost. Are you sure you wish to continue? (y/n)y
Migrating up to 20160122230602 from 20160122224707

  ++ migrating 20160122230602

     -> ALTER TABLE hello_user ADD active TINYINT(1) NOT NULL

  ++ migrated (0.07s)

  ------------------------

  ++ finished in 0.07s
  ++ 1 migrations executed
  ++ 1 sql queries

```
Now if we look at the database we have the new field ``active`` in the User table.

Our status looks like this:
```
root@metrics-test:/var/www/html# php bin/console doctrine:migrations:status

 == Configuration

    >> Name:                                               Application Migrations
    >> Database Driver:                                    pdo_mysql
    >> Database Name:                                      ormtest
    >> Configuration Source:                               manually configured
    >> Version Table Name:                                 migration_versions
    >> Version Column Name:                                version
    >> Migrations Namespace:                               Application\Migrations
    >> Migrations Directory:                               /var/www/orm_test/app/DoctrineMigrations
    >> Previous Version:                                   2016-01-22 22:47:07 (20160122224707)
    >> Current Version:                                    2016-01-22 23:06:02 (20160122230602)
    >> Next Version:                                       Already at latest version
    >> Latest Version:                                     2016-01-22 23:06:02 (20160122230602)
    >> Executed Migrations:                                3
    >> Executed Unavailable Migrations:                    0
    >> Available Migrations:                               3
    >> New Migrations:                                     0
```
and the `migration_versions` table looks like this:

|Version|
|:-------------|
|20160122223908|
|20160122224707|
|20160122230602|

which means these versions have been applied.

To migrate down to a different version `20160122224707` run:
```
root@metrics-test:/var/www/html# php bin/console doctrine:migrations:migrate 20160122224707

                    Application Migrations


WARNING! You are about to execute a database migration that could result in schema changes and data lost. Are you sure you wish to continue? (y/n)y
Migrating down to 20160122224707 from 20160122230602

  -- reverting 20160122230602

     -> ALTER TABLE hello_user DROP active

  -- reverted (0.1s)

  ------------------------

  ++ finished in 0.1s
  ++ 1 migrations executed
  ++ 1 sql queries
```
We've now moved back a version in the database and the changes are reflected in the schema.  The `migration_versions` table now looks like this:

|Version|
|:-------------|
|20160122223908|
|20160122224707|
|20160122230602|



