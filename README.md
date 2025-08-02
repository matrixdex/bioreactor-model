# Bioreactor-model
Bioreactor-model is a Python package for bioreactor and biomass growth calculations

## Installation

```bash
pip install bioreactor-model
```

## Classes
`calc.CellComposition()` calculates biomass formula and biomass formula weight

`calc.BiomassComposition()` solves biomass equation

### `CellComposition(values=dict(), dry_weights=False, molecular_formula=False)`
`CellComposition()` contains biomass elemental composition and molar weight. `CellComposition()` requires initialization in one of 2 ways. There are 2 ways to provide input to initialize biomass composition:

1. Use `dry_weights = True`: Store in values dictionary the dry weight percentages of carbon, hydrogen, nitrogen and oxygen, and ash fraction in biomass (obtained experimentally). See `values dict()` and `if dry_weights == True` below.  
2. Use `molecular_formula = True`: Store in values dictionary the molecular formula of biomass as shown below. See `values dict()` and `if molecular_formula == True` below. 

### `Values dict()`
`Values` is a required input in `CellComposition()`. `molecular_formula` or `dry_weights` variable determines how to `values dict()` is used. It stores 5 key-value pairs for carbon, hydrogen, oxygen, nitrogen and ash_fraction.

`if dry_weights == True`, values stores dry-weight percentages between 1 and 100.
1. `values['C']`: dry weight percentage of carbon (between 0 and 100)
2. `values['H']`: dry weight percentage of hydrogen (between 0 and 100)
3. `values['N']`: dry weight percentage of nitrogen (between 0 and 100)
4. `values['O']`: dry weight percentage of oxygen (between 0 and 100)
5. `values['ash_fraction']`: dry weight percentage of ash (between 0 and 100)

```bash
values = {
  'C':47,
  'H':4.15,
  'N':10.
  'O':31,
  'ash_fraction': 7.85
}
```

`if molecular_formula == True`, values stores molecular formula of biomass.
1. `values['C']`: default 1 mol carbon
2. `values['H']`: molecular subscript of hydrogen in biomass formula (mol hydrogen in biomass normalized to 1 mol carbon)
3. `values['N']`: molecular subscript of nitrogen in biomass formula (mol nitrogen in biomass normalized to 1 mol carbon)
4. `values['O']`: molecular subscript of oxygen in biomass formula (mol hydrogen in oxygen normalized to 1 mol carbon)
5. `values['ash_fraction']`: dry weight percentage of ash (between 0 and 100)

```bash
values = {
  'C':1,
  'H':1.66,
  'N':0.194
  'O':0.269,
  'ash_fraction': 7.85
}
```

Use `calc.CellComposition` class to obtain biomass formula and biomass formula weight.

`CellComposition(C_dry_weight, H_dry_weight, N_dry_weight, O_dry_weight, ash_fraction)` object requires as input:
1. `C_dry_weight`: dry weight percentage of carbon (between 0 and 100)
2. `H_dry_weight`: dry weight percentage of hydrogen (between 0 and 100)
3. `N_dry_weight`: dry weight percentage of nitrogen (between 0 and 100)
4. `O_dry_weight`: dry weight percentage of oxygen (between 0 and 100)
5. `ash_fraction`: dry weight percentage of ash (between 0 and 100)

These percentages must be between 0 and 100, not between 0 and 1. These are not absolute fractions but percentages.

### Estimating biomass formula

Function `CellComposition.biomass_formula()` returns dictionary with molecular formula of biomass. Dictionary keys are carbon, hydrogen, nitrogen and oxygen. Dictionary values are numbers corresponding to subscript value of each element in the biomass formula.

### Estimating biomass formula weight

Function `CellComposition.biomass_formula_weight()` returns molecular weight of biomass.

## Future development


Scale up parameters, fed-batch yield estimation, yield coefficient calculation, gas transfer estimation, oxygen transport and uptake rates will be added soon.





