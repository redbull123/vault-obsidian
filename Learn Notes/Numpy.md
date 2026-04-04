# Adding and Deleting data
- np.concatenate((array1,array2))
- np.concatenate((array1,array2), axis=1)
**To concatenate** bot array should be the same shape.
- array_1D.reshape((3, 1))
**Deleting with np.delete()** 
- np.delete(array_data, 1, axis=0)
**Where**
- np.where()
- private_block_indices = np.where(tree_census[:, 1] == 313879)

# Summarize
- .sum(axis=0) or, sum(axis=1,  keepdims=True)
- .min()
- .max() or max(axis=1)
- .mean() or mean(axis=1)
- .cumsum(axis=0) -> Devuelve la suma acumulado  