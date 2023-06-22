---
author: Me
title: Como mantener integridad de los datos con Ruby on Rails y Postgres
date: 2023-06-21
description: Como implementar funcionalidades de postgres con Ruby on Rails para un correcto diseño de la base de datos
categories: ["postgres", "ruby", "web"]
ShowToc: true
TocOpen: true
---
Este post es una adaptación a Ruby on Rails del post [con ejemplos en Elixir y  Phoenix](https://sergiovp.dev/blog/como-mantener-integridad-de-los-datos-con-elixir-phoenix-y-postgres/).

Una de las formas más comunes de almacenar datos en software es utilizando bases de datos relaciones. Aún y con el surgimiento de propuestas como las bases de datos NoSQL, el modelo relacional sigue estando presente porque es útil para la mayoría de los casos.

¿Qué es la integridad de los datos?

Se refiere a que la información almacenada en una base de datos sea completa y correcta. Cuando insertamos, actualizamos o eliminamos información la integridad puede perderse.

Existen distintos tipos de integridad:

- Integridad de dominio: Que los datos obligatorios estén presentes y sean del tipo esperado.
- Integridad de entidad: establece que la clave primaria de una tabla debe tener un valor único para cada fila de la tabla.
- La integridad referencial garantiza que la relación entre dos tablas permanezca sincronizada durante las operaciones y se asegura que los registros de las tablas relacionadas son válidos.

Vamos a imaginar el caso de un banco que tiene las siguientes tablas: `users`, `accounts` `deposits`, `withdrwals`.

Primero vamos a crear las migraciones que van a crear nuestras tablas
```bash
rails g model User email:string name:string
rails g model Account balance:decimal active:boolean user_id:references:users
rails g model Deposit amount:decimal account_id:references:accounts
rails g model Withdrawal amount:decimal account_id:references:accounts
```

Por ahora tenemos las siguientes migraciones:

```ruby
class CreateUsers < ActiveRecord::Migration[7.0]
  def change
    create_table :users do |t|
      t.string :email
      t.string :name
      t.timestamps
    end
  end
end
```

```ruby
class CreateAccounts < ActiveRecord::Migration[7.0]
  def change
    create_table :accounts do |t|
      t.decimal :balance
      t.boolean :active
      t.references :user, null: false, foreign_key: true
      t.timestamps
    end
  end
end
```

```ruby
class CreateDeposits < ActiveRecord::Migration[7.0]
  def change
    create_table :sales do |t|
      t.decimal :amount
      t.references :account, null: false, foreign_key: true

      t.timestamps
    end
  end
end
```

```ruby
class CreateWithdrawals < ActiveRecord::Migration[7.0]
  def change
    create_table :withdrawal do |t|
      t.decimal :amount
      t.references :account, null: false, foreign_key: true

      t.timestamps
    end
  end
end
```
## Campos vacíos o nulos

Como no queremos que un usuario pueda registrarse sin proporcionar su email y su nombre, forzaremos esto en la base de datos agregando la restricción `null: false` a la definición de las columnas.

```ruby
class CreateUsers < ActiveRecord::Migration[7.0]
  def change
    create_table :users do |t|
      t.string :email, null: false
      t.string :name, null: false
      t.timestamps
    end
  end
end
```

Otro lugar importante donde evitar la presencia de valores nulos es en las llaves foráneas. Además de incluir la misma opción en otros campos donde sea necesario:

```ruby
class CreateAccounts < ActiveRecord::Migration[7.0]
  def change
    create_table :accounts do |t|
      t.decimal :balance, null: false
      t.boolean :active, null: false
      t.references :user, null: false, foreign_key: true
      t.timestamps
    end
  end
end
```

```ruby
class CreateDeposits < ActiveRecord::Migration[7.0]
  def change
    create_table :deposits do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true

      t.timestamps
    end
  end
end
```

```ruby
class CreateWithdrawals < ActiveRecord::Migration[7.0]
  def change
    create_table :withdrawals do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true

      t.timestamps
    end
  end
end
```

## Valores por defecto

En algunos casos como los [campos booleanos es buena práctica definir un valor por defecto](https://sergiovp.dev/blog/siempre-asigna-un-valor-por-defecto-a-los-campos-booleanos/). Como es el caso del campo `active` en la tabla `accounts`.

Además, considerando que tenemos un campo `balance` que almacena el balance de la cuenta, podríamos asumir que nuestro caso de uso es que al crear una cuenta se cree con un balance de 0 (no nulo). De esta forma tendremos que lidiar con menos validaciones si queremos realizar operaciones aritméticas con dicho valor.

```ruby
class CreateAccounts < ActiveRecord::Migration[7.0]
  def change
    create_table :accounts do |t|
      t.decimal :balance, null: false, default: 0
      t.boolean :active, null: false, default: false
      t.references :user, null: false, foreign_key: true
      t.timestamps
    end
  end
end
```

## Campos únicos

En la tabla `users` guardamos información como el email y el nombre del usuario. Como no queremos que haya 2 usuarios registrados con el mismo email, vamos a agregar esta restricción en nuestra tabla:

```ruby
class CreateUsers < ActiveRecord::Migration[7.0]
  def change
    create_table :users do |t|
      t.string :email, null: false
      t.string :name, null: false
      t.timestamps
    end

    add_index :users, :email, unique: true
  end
end
```

La línea `add_index :users, :email, unique: true` hace 2 cosas:
1. Crea un índice en la columna `email` de la tabla users
2. Este índice es único

Esto hará que nuestra base de datos responda con un error si intentamos crear un registro con el mismo valor de `email` que un registro existente.

Otra funcionalidad útil es que podemos crear esta misma restricción con múltiples columnas. Es decir, negar la creación de un registro donde la combinación de dos o más columnas sea igual al de otro registro. Solo hay que agregar las columnas:

```ruby
add_index :users, [:email, :name], unique: true
```

## Registros huérfanos

En caso de que permitamos que los usuarios eliminen registros de la base de datos o esto suceda por algún proceso interno, podríamos perder integridad referencial.

Por ejemplo, si eliminamos una cuenta de nuestra base de datos, puede que los depósitos y retiros de dicho usuario permanezcan, pero sin saber a que cuenta hacían referencia.

Tenemos varias opciones:
- `:cascade` Elimina todos los registros relacionados. Alias de la opción `CASCADE`
- `:nullify` Establece la llave foránea en todos los registros relacionados en NULL.
- `:restrict` Genera un error si se intenta eliminar un registro padre que tiene registros relacionados.

```ruby

class CreateAccounts < ActiveRecord::Migration[7.0]
  def change
    create_table :accounts do |t|
      t.decimal :balance, null: false, default: 0
      t.boolean :active, null: false, default: false
      t.references :user, null: false, foreign_key: true, foreign_key: {on_delete: :restrict}
      t.timestamps
    end
  end
end
```

```ruby
class CreateDeposits < ActiveRecord::Migration[7.0]
  def change
    create_table :deposits do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true, foreign_key: {on_delete: :restrict}

      t.timestamps
    end
  end
end
```

```ruby
class CreateWithdrawals < ActiveRecord::Migration[7.0]
  def change
    create_table :withdrawals do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true, foreign_key: {on_delete: :restrict}

      t.timestamps
    end
  end
end
```

La mejor opción dependerá de nuestro caso de uso. Por ahora evitaremos su eliminación usando la opción `on_delete: :restrict`.

## Valores inválidos

Por ejemplo, cuando se manejan valores monetarios, como en la tabla donde registramos los depósitos y la tabla de retiros.  En el caso del campo `balance` de la tabla `accounts` un valor menor a 0 podríamos considerarlo inválido.

Además, en el caso de los depósitos y retiros, no tiene mucho sentido que el monto de una de estas operaciones sea 0 y mucho menos menor a 0.

```ruby
class CreateDeposits < ActiveRecord::Migration[7.0]
  def change
    create_table :deposits do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true, foreign_key: {on_delete: :restrict}

      t.timestamps
    end

    reversible do |dir|
      dir.up do
        execute <<-SQL
		  ALTER TABLE deposits
	      ADD CONSTRAINT deposits_amount_must_be_positive
	      CHECK (amount > 0)
        SQL
      end

      dir.down do
        execute <<-SQL
          ALTER TABLE deposits
          DROP CONSTRAINT deposits_amount_must_be_positive
        SQL
      end
	  end
  end
end
```

```ruby
class CreateWithdrawals < ActiveRecord::Migration[7.0]
  def change
    create_table :withdrawals do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true, foreign_key: {on_delete: :restrict}

      t.timestamps
    end

    reversible do |dir|
      dir.up do
        execute <<-SQL
		  ALTER TABLE withdrawals
	      ADD CONSTRAINT withdrawals_amount_must_be_positive
	      CHECK (amount > 0)
        SQL
      end

      dir.down do
        execute <<-SQL
          ALTER TABLE withdrawals
          DROP CONSTRAINT withdrawals_amount_must_be_positive
        SQL
      end
	  end
  end
end
```

## Valores inválidos entre tablas

Cuando tenemos una acción de retiro, asumimos que la cantidad que se retira se resta del balance de la cuenta de la cual se efectúa el retiro.

Para evitar que se realice un retiro por un monto mayor al balance de la cuenta podemos agregar una verificación:

```ruby
class CreateWithdrawals < ActiveRecord::Migration[6.1]
  def change
    create_table :withdrawals do |t|
      t.decimal :amount, null: false
      t.references :account, null: false, foreign_key: true, foreign_key: {on_delete: :restrict}

      t.timestamps
    end

    reversible do |dir|
      dir.up do
      #...previous constrains

        execute <<-SQL
          CREATE OR REPLACE FUNCTION validate_withdrawal_amount()
          RETURNS TRIGGER AS $$
            BEGIN
              IF NEW.amount > (SELECT balance FROM accounts WHERE id = NEW.account_id) THEN
              RAISE EXCEPTION 'Withdrawal amount is greater than account balance';
              END IF;
              RETURN NEW;
            END;
            $$ LANGUAGE plpgsql;
        SQL

        execute <<-SQL
          CREATE TRIGGER withdrawal_amount_validation
          BEFORE INSERT ON withdrawals
          FOR EACH ROW EXECUTE FUNCTION validate_withdrawal_amount();
        SQL
      end

      dir.down do
        execute <<-SQL
          DROP TRIGGER withdrawal_amount_validation IF EXISTS ON withdrawals;
        SQL

        execute <<-SQL
          DROP FUNCTION validate_withdrawal_amount();
        SQL
      end
    end
  end
end
```

Dado que en estos ejemplos se está usando Postgres como base de datos, debemos crear una función que se encargue de realizar la verificación, además de un trigger que se encargue de llamar a dicha función cada vez que se realice un `insert` en la tabla `withdrawals`.

Esto debido a que PostgreSQL no soporta *sub queries* en los `CONSTRAINT`.

## Operaciones parciales

En caso de que un usuario realice un retiro, hay 2 operaciones que deben realizarse:
 1. Crear el registro en la tabla `withdrawals` con la información del retiro
 2. Actualizar el balance en la tabla `accounts` restando el monto del retiro al balance de la cuenta

Queremos evitar que una de las operaciones se complete correctamente, pero la otra no. Quedando la base de datos con un estado incorrecto. Por ejemplo, tener un registro de retiro, pero que este monto no sea restado del balance de la cuenta.

Esto lo logramos con transacciones de la base de datos:

```ruby
ActiveRecord::Base.transaction do
  withdrawal.save!
  withdrawal.account.subtract_balance!(withdrawal.amount)
end
```

De esta forma nos aseguramos que ambas operaciones se ejecuten correctamente o de lo contrario se lanzara una excepción, realizando un rollback y ninguno de los cambios será confirmado.

Así como estos escenarios hipotéticos, pueden surgir muchos más dependiendo de información que estemos almacenando en nuestra aplicación.

También es importante distinguir los casos donde una restricción es más una regla de negocio que puede cambiar o ajustarse de manera más o menos frecuente. Se debe evaluar si la base de datos es el lugar correcto para dicha restricción o, por el contrario, nos restaría flexibilidad en el futuro y la capa de dominio sería un mejor lugar para manejarla.

¿Qué hay de agregar estas validaciones en los modelos/dominio? Seguro, pero como menciona David Bryant Copeland en su libro [Sustainable Web Development with Ruby on Rails](https://sustainable-rails.com/): Las validaciones son geniales para la experiencia de usuario, pero estas no proporcionan integridad de los datos.1

## Referencias
[Avoiding Data Loss: Understanding the `:on_delete` Option in Elixir Migrations](https://doriankarter.com/avoiding-data-loss-understanding-the-ondelete-option-in-elixir-migrations/)
[The difference between on_delete: :restrict, :nullify, and :cascade](https://ashton.codes/the-difference-between-on_delete-restrict-nullify-and-cascade/)
