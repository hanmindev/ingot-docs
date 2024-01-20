# Bundling Data

There are two ways to bundle data in Ingot:

1. Structs
2. Compounds

## Structs

Structs are a way to store multiple values in a single variable. The variables can be of different types.

### Declaration

Structs are defined with the `struct` keyword.

_src/main.ing_
```C
struct Team {
    points: int,
    name: string
}
```

### Instantiation

Instances of structs can be made by specifying the struct name and then the values for each field.

_src/main.ing_
```C
fn main() {
    let team: Team = {
        points: 0,
        name: "Team 1"
    };
}
```

Specific fields can be accessed with the `.` operator.

_src/main.ing_
```C
fn main() {
    let team: Team = {
        points: 0,
        name: "Team 1"
    };
    team.points = 10;
    
    println!(team.points);
    println!(team.name);
}
```

**Sample Output**
```
10
Team 1
```

### Methods

Structs can have methods. The syntax is similar to that of Rust:

_src/main.ing_
```C
fn main() {
    let team: Team = {
        points: 0,
        name: "Team 1"
    };
}

impl Team {
    fn add_points(&self, points: int) {
        self.points += points;
    }
}
fn main() {
    let team: Team = {
        points: 0,
        name: "Team 1"
    };
    team.add_points(10);
    
    println!(team.points);
    println!(team.name);
}
```

Note that here, even though `&self` is a reference value, we still use the dot notation. This is because the compiler will automatically dereference the reference value.

**Sample Output**
```
10
Team 1
```

## Compounds

Structs will store scoreboard-storable values in a scoreboard, and data storage-storable values in data storage.

They are not stored as compounds, meaning that in order to convert it into a compound to use in NBT, you need to convert it to a compound, which can be performance intensive.

Instead, if you want a type-safe compound, you may add a struct to a compound like so:

_src/main.ing_
```C
struct Point {
    x: int,
    y: int
}

fn main() {
    let c: compound<Point> = {
        x: 1,
        y: 2
    };
}
```

Now, if you try to access a field that does not exist, the compiler will give an error.

If you want to convert a compound to a struct, you can use the `to_struct` macro, and use the `to_compound` macro to convert a struct to a compound.

_src/main.ing_
```C
struct Point {
    x: int,
    y: int
}

fn main() {
    let c: compound<Point> = {
        x: 1,
        y: 2
    };
    
    let p: Point = to_struct!(c);
    
    let c2: compound<Point> = to_compound!(p);
}
```