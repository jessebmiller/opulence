# Architecture

Most things will be metadata driven and configured by Squad Games components and Format

## Algorythmic universe

Generates star systems from a seed and a set of components
- stars
- planets
- moons
- comets
- astroids
- other celestlial bodies

the Format config structures the celestial bodies in terms of what can be found
to orbit what like planets and astroids around stars, and stars around black holes

Including:
- how many
- at what distance
- in what direction
- at what speed

Represent the universe as an (indexed)[https://www.redblobgames.com/grids/hexagons/]
stack of planes of hex areas of some uniform thicknes. Generate a random seed
for each area by hashing the area index with the universe seed (format address?)

The format also specifies the dimentions of the areas

### Properties of areas
Specified in the format config each as a function of the area seed and coordinates
- security level (0-1)
- number of top level celestial bodies
- resource scaling factor (how much the base resources of the bodies in the area are
  scaled up or down)

### Celestial Bodies
Each body in an area is assigned coordinates and a seed by hashing the area seed with
the bodies coordinates.

Each celestial body component needs to take a seed and generate a map of the body
with things like:
- environmental conditions
- resources, how much, natural replenishment curve
- surface type (rock, mud, foo)
  - surface properties (roughness, moisture, bar)

Planets would generate surface maps
Nebulas would generate a 3d navigatible area

The components may need to include code to generate these maps
or the game may offer a declarative system for generating them

## Industry

The posibilities of industry, the items, how to make and repair them, what they do
are definied in a few different component types

### Recipes
specify how to make things
- ingrediants (items) that are consumed
- items that are required but are not consumed. For instance:
  - factory, machinery, bots, etc.
- how much wear and tear those items take during production
- environmental conditions that modify requirements or are required
- how long production takes
- Item that is produced

### Items
Things that exist and can perform actions on behalf of players
- actions they can perform (from among a catalog offered by the base game)
- wear and tear they can withstand
- wear and tear environment params
- wear and tear curve or function (age and environment params -> wear and tear)
- resources it decays into when worn out
- type (liquid, gas, solid, powder, corrosive, foo)
- volume
- weight
- labels

The Format config specifies sets of items players may choose from to start with
possibly can be extended to be a choice tree for starter items

## Actions
The base game provides action factories that take parameters specified by components
and return actions for the game to use
- contain items
  - max volume
  - max weight
  - allowable types
- travel across surfaces
  - allowable types
  - properties in speed curve
  - fuels in speed curve
  - speed curve
- travel across space
  - fules in speed curve
  - speed curve
- place items
  - max volume
  - max weight
  - allowable types
- manufacture a recipe
  - allowable recipes
  - max manufacture duration?
  - TODO how can we clasify recipes (or anything) to make this able to specify which
    recipes the action can and can't manufacture
- change environmental conditions
- expose environmental conditions to contained items
- extract resources
  - fuel params
  - environmental params
  - extraction rate curve (fuel params -> environmental -params -> extraction rate)
- damage items
- disable items
- restrict items actions
- modify items efficacy

### generic parameters for all actions
- requirements, may only be activated if requirements are met
  - consumed items
  - used items
  - numerical property range {prop: [min, max]}
    - like {prop-name<type-selector>: [min, max]}
    - must have all damage sum to less than 10% 
      - "{damage: [0%, 10%]}"
    - must have kenetic damage less than 10%
      - "{damage<kenetic>: [0%, 10%]}"
    - must have any environmental property other than heat or moisture above 50 
      - "{enviornmental<any,!heat,!moisture>: [50, infinity]"
    - must have all foo that are not bar or baz multiply to greater than 1
      - "{foo<all(*),!bar,!baz>: [-infinity: 1]}"
    - TODO consider breaking up the syntax above into a more descriptive dictionary like data structure
      - {name: damage, modifier: percent-of, modifier-params: hit-points min: 0, max 10}
      - {name: damage, types: [kenetic], modifier: percent-of, modifier-params: hit-points min: 0, max 10}
      - {name: environmental, types: any, filter-types: [heat, moisture], min: 50, max: infinity}
      - {name: foo, types: all, filter-types: [bar, baz], binary-operator: mult, min: -infinity, max: 1}
  - required labels
  - excluded labels
- numerical effect curves. An action can have an effect on some item's numerical properties according to this
  curve
  - target property modified
    - heat damage taken
  - actor property params
    - fuel type
  - target property params
    - heat damage taken
  - environment property params
    - tempreture
  - curve function :: (actor params, target params, environ params) -> target property new value

## Immutable physical rules
Some flexible system for creating, damaging, disabling, destroying, moving, and colliding items
- production rate
- total damage taken (typed)
- resistences (typed)
- resulting hit points
- position
- velocity

note: extraction from resources, and stacking/packaging can be done as recipes
