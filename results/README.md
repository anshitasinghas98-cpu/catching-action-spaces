# Results data

Every row is one trained policy (one seed) evaluated on a set of throws. `catch_rate` = `n_caught` / `n_throws`.

## catch_rate_per_seed.csv

Final policies of Groups A (Mobile Base) and B (Fixed Base), evaluated with deterministic actions on the fixed set of 500 throws at restitution 0.39. This is the data of the catch-rate figure on the project page.

| Column | Meaning |
|---|---|
| `group` | Group A (Mobile Base) or Group B (Fixed Base) |
| `action_space` | Arm action space |
| `learner` | PPO |
| `seed` | Training seed |
| `n_throws` | Number of throws (500) |
| `n_caught` | Number of catches |
| `catch_rate` | Fraction of throws caught |

## learning_curves_per_seed.csv

Catch rate during the catching stage, on the same 200 throws at restitution 0.39, at every saved checkpoint. The first checkpoint of each group is the end of the tracking stage, when the fingers are still held at a neutral pose. The last row of each seed (14 million steps in Group A and in the joint-observation check, 10 million steps in Groups B and C) is the final policy on the first 200 of the 500 evaluation throws.

| Column | Meaning |
|---|---|
| `group` | Group A (Mobile Base), Group A joint-observation check, Group B (Fixed Base) or Group C (learner comparison) |
| `action_space` | Arm action space |
| `learner` | PPO or TD-MPC2 |
| `seed` | Training seed |
| `steps_M` | Environment steps, in millions |
| `n_throws` | Number of throws (200) |
| `n_caught` | Number of catches |
| `catch_rate` | Fraction of throws caught |
