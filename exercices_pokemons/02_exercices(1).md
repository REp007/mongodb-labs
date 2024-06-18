### Querying and Aggregation Exercises

### 1. **Find All Pokémon of a Specific Type**:
    
    - Write a query to find all Pokémon that are of type 'Grass'.
```js
db.pokemons.find({type: 'Grass'})
```

### 2. **Pokémon with High Speed**:
    
    - Find all Pokémon with a speed greater than 70.
```js
db.pokemons.find({'stats.speed': 
    {$gt: 70}
}, {})
```
### 3. **Average HP by Type**:
    
    - Calculate the average HP of Pokémon for each type.
```js
db.pokemons.aggregate([
    {$unwind: '$type'},
    {$group: {_id: '$type', 
     avgHP: {$avg: '$stats.hp'}}}
]);
```

### 4. **Top Attackers**:
    
    - List the top 5 Pokémon with the highest attack stat.
```js
db.pokemons.find({},{}).sort({'stats.attack': -1}).limit(5);
```
### 5. **Multiple Types Pokémon**:
    
    - Find all Pokémon that have more than one type.
```js
db.pokemons.find({ $where: 'this.type.length > 1'});
```
### 6. **Count Pokémon by Generation**:
    
    - Count the number of Pokémon introduced in each generation.
```js
db.pokemons.aggregate([
    {$unwind: '$moves.level'},
    {$group: {_id: '$moves.level.gen', count: {$sum : 1}}}
])
```
### 7. **Gender Ratio**:
    
    - Find Pokémon with a female ratio greater than 50%.
```js
db.pokemons.find({ 'misc.sex.female': { $gt: 50 } });

```
### 8. **Damage Resistance**:
    
    - List Pokémon that take less than 1x damage from 'Fire' type moves.
```js
db.pokemons.find({ 'damages.fire': { $lt: 1 } });
```
### 9. **Highest Base Stat Total**:
    
    - Find the Pokémon with the highest sum of all base stats (HP, attack, defense, `spattack`, spdefense, speed).
```js

db.pokemons.aggregate([
    {
        $project: {
            name: 1,
            totalStates: {
                $sum: ['$stats.hp', '$stats.attack', '$stats.defense', '$stats.spattack', '$stats.spdefense', '$stats.speed']
            }
        }
    },
    {$sort: {totalStats: -1}},
    {$limit: 1}
])

```
### 10. **Common Moves**:
    
    - List moves that are common across at least 10 different Pokémon.

```js
db.pokemons.aggregate([
    {$unwind: '$moves.level'},
    {$group: {_id: '$moves.level.name', count: {$sum: 1}}},
    {$match: {count: {$gte: 10}}}
])
```