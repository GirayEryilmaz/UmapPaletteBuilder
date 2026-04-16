Here is a pyhton script to export your own umap (and colors):

```
def export_umap_palette(adata, cluster_key, color_key=None, umap_key="X_umap", output="palette.csv"):
    """Export cluster, UMAP x, UMAP y, and colors from an AnnData object.

    Parameters
    ----------
    adata : anndata.AnnData
    cluster_key : str
        Column in adata.obs with cluster labels (e.g. "leiden").
    color_key : str, optional
        Key in adata.uns (e.g. "leiden_colors"). If None, defaults to
        "{cluster_key}_colors".
    umap_key : str
        Key in adata.obsm for UMAP coordinates. Default "X_umap".
    output : str
        Output CSV path.
    """
    import scanpy as sc

    if color_key is None:
        color_key = f"{cluster_key}_colors"

    df = sc.get.obs_df(adata, keys=[cluster_key], obsm_keys=[(umap_key, 0), (umap_key, 1)])
    df.columns = ["cluster", "x", "y"]

    if color_key in adata.uns:
        categories = adata.obs[cluster_key].cat.categories
        color_map = dict(zip(categories, adata.uns[color_key]))
        df["color"] = df["cluster"].map(color_map)

    df.to_csv(output, index=False)
    print(f"Wrote {len(df)} rows to {output}")

# Usage

import scanpy as sc
adata = sc.read_h5ad('pbmc.h5ad')
export_umap_palette(adata, cluster_key="clusters")
```
