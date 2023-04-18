---
author: Me
title: Siempre asigna un valor por defecto a los campos booleanos
date: 2023-04-18
description: Porque es importante que los campos booleanos en nuestra base de datos no permitan valores nulos?
math: true
categories: ["postgres", "web"]
---

Es relativamente común ver campos booleanos en una base de datos para representar algún estado y al mismo tiempo es igualmente común ver que por un descuido esta columna pueda y llegue a tener valores nulos.

Esto puede acarrear errores de semántica y de lógica. Para evitar este problema es una buena práctica definir los campos como `null: false` y además siempre dar un valor por defecto, por ejemplo `admin: false`.

```ruby
create_table :users do |t|
  t.boolean :admin, null: false, default: false
end
```

Si un campo es booleano, entonces su valor es `true` o `false`.

Pero, si tú, como yo en algún momento, pensaste en usar el valor `null` como un tercer estado, quizás lo que busques es un campo `enum`:

```ruby
create_table :users do |t|
  t.integer :role, null: false, default: 1
end
```

```ruby
class User < ApplicationRecord
  enum :role, [:user, :admin]
end
```

o incluso una `lookup table`:

```ruby
create_table :roles do |t|
  t.string :role, null: false
end
```

```ruby
create_table :users do |t|
  t.references :role, null: false, foreign_key: true
end
```

La mejor opción dependerá del contexto de tu aplicación.

Un excelente post sobre el tema es [Avoid the Three-state Boolean Problem](https://thoughtbot.com/blog/avoid-the-threestate-boolean-problem)
