# Structs

Structs are a way to store multiple values in a single variable. It is like a compound except that it is type safe, but more restrictive.

## Declaration

Structs are declared with the `struct` keyword.

_src/main.ing_
```C
struct Team {
    int points;
    string name;
}
```

Instances of structs can be made by specifying the struct name and then the values for each field.

_src/main.ing_
```C
fn main() {
    Team team = {
        points: 0,
        name: "Team 1"
    };
}
```

Specific fields can be accessed with the `.` operator.

_src/main.ing_
```C
fn main() {
    Team team = {
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

## Methods

Structs can have methods. The syntax is similar to that of Rust:


_src/main.ing_
```C
fn main() {
    Team team = {
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
    Team team = {
        points: 0,
        name: "Team 1"
    };
    team.add_points(10);
    
    println!(team.points);
    println!(team.name);
}
```

**Sample Output**
```
10
Team 1
```