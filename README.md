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
2. `values['H']`: hydrogren subscript in biomass formula (mol hydrogen normalized to 1 mol carbon)
3. `values['N']`: nitrogen subscript in biomass formula (mol nitrogen normalized to 1 mol carbon)
4. `values['O']`: oxygen subscript in biomass formula (mol hydrogen normalized to 1 mol carbon)
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

#### `CellComposition()` returns `None` if `dry_weights == False and molecular_formula == False`

### `CellComposition().biomass_composition`
Returns dictionary with biomass composition. `CellComposition().biomass_composition['formula']` is biomass molecular formula string.

Example

```bash
CellComposition().biomass_composition = {
  'C':1,
  'H':1.66,
  'N':0.194
  'O':0.269,
  'formula': 'C(H-1.66)(N-0.194)(O-0.269)'
}
```

### `CellComposition().biomass_molar_weight`
Returns numerical molar weight of biomass

Example

```bash
CellComposition().biomass_molar_weight = 22.48
```

### `BiomassEquation(biomass_composition=dict(), substrate='glucose')`
`BiomassEquation()` object solves biomass growth equations. This class must be initialized by biomass composition. This is obtained from `CellComposition().biomass_composition` or provided manually as a dictionary (see below).

#### Biomass composition input schema

```bash
biomass_composition = {
  'C':1,
  'H':1.66,
  'N':0.194
  'O':0.269,
}
```

### `substrate='glucose'`
Glucose is default substrate for biomass growth. To change to hexane, use
```bash
hexane_biomass_growth = BiomassEquation(biomass_composition, substrate='hexane')
```

Biomass growth equation has 4 equations and 5 variables (a,b,c,d,e). Additional equation is needed to determine solution with equation linear solver. This additional equation can provided in 4 ways. There are 4 ways to solve biomass growth equation in `bioreactor-model`:

#### 1. `Function BiomassEquation.set_gas_io_values(inlet_N2, inlet_O2, outlet_CO2, outlet_N2, outlet_O2)`
Uses percentage values. Molar values must be normalized to percentages first

This function sets gaseous inlet-outlet exchange percentage values. One of `inlet_N2` or `inlet_O2` may be omitted. Omitted variable is determined automatically by substracting from 100% the provided input value, as these values are percentages. Similarly, only one of `outlet_CO2, outlet_N2, outlet_O2` may be omitted and is determined autoamtically by substracting from 100% the 2 provided input values. This is used to determine respiratory quotient, providing an equation with b and d terms, and serving as 5th equation to solve linear system of biomass growth equations.

After providing gaseous inlet and output values, `BiomassEquation().solve_biomass_equation()` can be used to find biomass growth equation solution.

#### 2. `Function BiomassEquation.solve_biomass_equation(biomass_yield_gram = biomass_yield_gram, biomass_molar_weight=biomass_molar_weight)`:

The function `solve_biomass_equation()` is provided 2 inputs:
1. `biomass_yield_gram`: This is gram biomass / gram substrate yield (by mass). This value is experiemntally obtained and directly provided as input to biomass_growth solver.
 
2. `biomass_molar_weight`: This value is obtained from `CellComposition().biomass_molar_weight`. `biomass_molar_weight` and `biomass_yield_gram` calculate molar yield of biomass per mole of substrate, equal to c in the linear biomass growth equations. This is used as the 5th equation in the linear biomass growth equations.

#### 3. `Function BiomassEquation.solve_biomass_equation(biomass_yield_mol = biomass_yield_mol)`:   

The function `solve_biomass_equation()` is provided `biomass_yield_mol` input. This is the molar yield of biomass per mole of substrate. It is equal to c in the linear biomass growth equations. No further calculation is needed because molar yield is provided directly as input.

#### 4. `Function BiomassEquation.solve_biomass_equation(rq = respiratory_quotient)`:

Respiratory quotient (RQ) is directly provided as input to the biomass growth equation solver. `RQ = d / b`. With RQ as input, this equation is used as 5th equation in linear biomass growth equation.

### `BiomassEquation.solve_biomass_equation()`

Use one of the above 4 methods to find a solution to biomass growth. Returns string form of biomass growth equation. Running this function also stores biomass growth solution in `BiomassEquation().biomass_growth_solution`.

Example

```bash
BiomassEquation().solve_biomass_equation = "C6H12O6 + 0.078NH3 + 5.546O2 -> 0.4C(H-1.660)(N-0.194)(O-0.269) + 5.6CO2 + 5.784H2O"
```

### `BiomassEquation.biomass_equation_solution`

Return dictionary with solution of biomass growth equation. It can only be run after `BiomassEquation.solve_biomass_equation()`. This dictionary stores 4 key-value pairs:

1. `biomass_equation_solution['string']`: `string` of biomass growth equation with molar coefficients determined with biomass growth equation solver
2. `biomass_equation_solution['molar_coeff']` `dict()` of molar coefficients in biomass growth equation. Stores molar coefficients for NH3, O2, biomass, CO2 and H2O. These are also solutions (a,b,c,d,e) of linear system of biomass growth equation respectively. See example for `biomass_equation_solution['molar_coeff']` schema.
3. `biomass_composition`: `dict()` of biomass elemental compositions. Equivalent to `CellComposition().biomass_composition`.
4. `rq`: `float` numerical value of respiratory quotient of biomass growth

Example

```bash
biomass_equation_solution = {'string': 'C6H12O6 + 0.078NH3 + 5.546O2 -> 0.4C(H-1.660)(N-0.194)(O-0.269) + 5.6CO2 + 5.784H2O', 'molar_coeff': {'NH3': 0.078, 'O2': 5.546, 'biomass': 0.4, 'CO2': 5.6, 'H2O': 5.784}, 'biomass_composition': {'C': 1, 'H': 1.66, 'N': 0.194, 'O': 0.269, 'formula': 'C(H-1.660)(N-0.194)(O-0.269)'}, 'rq': 1.01}
```

## Future development


Scale up parameters, fed-batch yield estimation, yield coefficient calculation, gas transfer estimation, oxygen transport and uptake rates will be added soon.











