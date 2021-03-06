## RDKit IPython Tools
by Axel Pahl

### Work in progress
*(10-Feb-2016)*
In the past months, this set of tools based on the [RDKit](http.//www.rdkit.org) has evolved quite a bit.


#### Module tools

A Mol_List class was introduced, which is a subclass of a Python list for holding lists of RDKit molecule objects and allows direct access to a lot of the RDKit functionality.
It is meant to be used with the Jupyter Notebook and includes a.o.:
* display of the Mol_List
    * as HTML grid
  (both display types include the option to select molecules by clicking)
* display of a summary including number of records and min, max, mean, median for numeric properties
* display of correlations between the Mol_List's properties
  (using np.corrcoef, this allows getting a quick overview on which properties correlate with each other)
* methods for sorting, searching (by property or substructure) and filtering the Mol_List
* methods for renaming, reordering and calculating properties
* direct plotting of properties as publication-grade [Highcharts](http://www.highcharts.com/) *or* [Bokeh](http://bokeh.pydata.org/en/latest/) plots with **structure tooltips** (!).
    * the plotting functionalities reside in their own module and can also be used for plotting Pandas dataframes and Python dicts.
    * further development will focus on Bokeh because of the more pythonic interface


##### Other functions in the tools module:
- *jsme*: Display Peter Ertl's Javascript Molecule Editor to enter a molecule directly in the IPython notebook (*how cool is that??*)

plus many others

#### Module pipeline

A Pipelining Workflow using Python Generators, mainly for RDKit and large compound sets.
The use of generators allows working with arbitrarily large data sets, the memory usage at any given time is low.

Example use:

    >>> from rdkit_ipynb_tools import pipeline as p
    >>> s = Summary()
    >>> rd = start_csv_reader(test_data_b64.csv.gz", summary=s)
    >>> b64 = pipe_mol_from_b64(rd, summary=s)
    >>> filt = pipe_mol_filter(b64, "[H]c2c([H])c1ncoc1c([H])c2C(N)=O", summary=s)
    >>> stop_sdf_writer(filt, "test.sdf", summary=s)

or, using the pipe function:

    >>> s = Summary()
    >>> rd = start_sdf_reader("test.sdf", summary=s)
    >>> pipe(rd,
    >>>      pipe_keep_largest_fragment,
    >>>      (pipe_neutralize_mol, {"summary": s}),
    >>>      (pipe_keep_props, ["Ordernumber", "NP_Score"]),
    >>>      (stop_csv_writer, "test.csv", {"summary": s})
    >>>     )

The progress of the pipeline can be followed in a separate terminal with: `watch -n 2 cat pipeline.log`

#### Currently Available Pipeline Components:
| Starting                   | Running                    | Stopping
|----------------------------|----------------------------|---------------------------|
| start_cache_reader         | pipe_calc_props            | stop_cache_writer         |
| start_csv_reader           | pipe_custom_filter         | stop_count_records        |
| start_mol_csv_reader       | pipe_custom_man            | stop_csv_writer           |
| start_sdf_reader           | pipe_do_nothing            | stop_dict_from_stream     |
| start_stream_from_dict     | pipe_has_prop_filter       | stop_mol_list_from_stream |
| start_stream_from_mol_list | pipe_id_filter             | stop_sdf_writer           |
|                            | pipe_inspect_stream        |                           |
|                            | pipe_join_data_from_file   |                           |
|                            | pipe_keep_largest_fragment |                           |
|                            | pipe_keep_props            |                           |
|                            | pipe_merge_data            |                           |
|                            | pipe_mol_filter            |                           |
|                            | pipe_mol_from_b64          |                           |
|                            | pipe_mol_from_smiles       |                           |
|                            | pipe_mol_to_b64            |                           |
|                            | pipe_mol_to_smiles         |                           |
|                            | pipe_neutralize_mol        |                           |
|                            | pipe_remove_props          |                           |
|                            | pipe_rename_prop           |                           |
|                            | pipe_sim_filter            |                           |

Limitation: unlike in other pipelining tools, because of the nature of Python generators, the pipeline can not be branched.

### Tutorial
Much of the functionality is shown in the [tutorial notebook](tutorial/tutorial.ipynb).

### ToDo
This README is meant as a teaser. I urgently need to prepare a proper example notebook to show all the functionality, this is high on my ToDo list.

### Documentation
The module documentation can be built with sphinx using the `make_doc.sh` script
