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
