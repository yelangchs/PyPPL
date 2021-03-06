# Templating

`PyPPL` has its own template engine, which is derived from a [500-line-or-less template engine][1]. It also supports [Jinja2][2] if you have it installed and specify `"Jinja2"` to `pXXX.template`. The built-in template engine is enabled by default.

## Common data avaible for rendering
When rendering a template, following data are fed to the render function. So that you can use those values in the template. Some attribute values of a process are shared for all templates that are applied:

* `proc.aggr`: The aggregation name of the process
* `proc.args`: A `dict` of process arguments. To access an item of it: `{{args.<item>}}`
* `proc.cache`: The cache option
* `proc.cclean`: Whether clean (error check and export) the job when it's cached?
* `proc.desc`: The description of the process
* `proc.echo`: The echo option
* `proc.errhow`: What to do if error happens
* `proc.errntry`: If `errorhow == 'retry'`, how many times to re-try if a job fails
* `proc.exdir`: The export directory
* `proc.exhow`: How to export output files
* `proc.exow`: Whether to overwrite existing files when exporting output files
* `proc.forks`: How many jobs to run concurrently
* `proc.id`: The id of the process.
* `proc.infile`: Where does `{{in.infile}}` refer to? (including other input files)
* `proc.lang`: The interpreter for the script
* `proc.ppldir`: Where the workdirs are located
* `proc.procvars`: The `dict` of all avaiable attributes of a process, can be accessed directly by `{{proc.<var>}}`
* `proc.rc`: The rc option
* `proc.resume`: The resume option
* `proc.runner`: The runner
* `proc.sets`: A list of attribute names that has been set explictly
* `proc.size`: Number of jobs
* `proc.suffix`: The unique suffix of the process
* `proc.tag`: The tag of the process
* `proc.workdir`: The workdir of the process

## Other data for rendering
For each job, we also have some value available for rendering:

* `job.index`: The index of the job
* `job.indir`: The input directory of the job
* `job.outdir`: The output directory of the job
* `job.dir`: The directory of the job
* `job.outfile`: The stdout file of the job
* `job.errfile`: The stderr file of the job
* `job.pidfile`: The file stores the PID of the job or the identity from a queue runner.

Input and output data are under namespace `in` and `out`, respectively.
For example, you have following definition:
```python
pXXX.input  = {"a": ["hello"], "b": ["/path/to/file"]}
pXXX.output = "a:{{in.a}} world!"
```
Now you can access them by: `{{in.a}}`, `{{in.b}}` and `{{out.a}}`

## The scope of data
|Attribute|Data available|Meaning|
|---------|------------|-------|
|`pXXX.beforeCmd`|`{{proc.*}}`|Command to run before job starts|
|`pXXX.afterCmd`|`{{proc.*}}`|Command to run after job finishes|
|`pXXX.brings`|`{{proc.*}}`, `{{job.*}}`, `{{in.*}}`|The bring-in files|
|`pXXX.output`|`{{proc.*}}`, `{{job.*}}`, `{{in.*}}`, `{{bring.*}}`|The output of the process|
|`pXXX.expect`|All above-mentioned data|Command to check output|
|`pXXX.expart`|All above-mentioned data|Partial export|
|`pXXX.script`|All above-mentioned data|The script to run|

## Built-in functions
Sometimes we need to transform the data in a template. We have some built-in functions available for the transformation.  
For built-in template engine, you may use pipe, for example: `{{in.file | basename}}`; for `Jinja2`, you have to use functions as "functions", for example: `{{basename(in.file)}}`. Here we give the examples with built-in template engine syntax.

- `R`: Transform a python value to R value. For example:

| Usage | Data | Result |
|-------|------|--------|
| `{{v `&#x7c;` R}}` | `{'v': True}` | `TRUE` |
|| `{'v': 'TRUE'}` | `TRUE` | 
|| `{'v': 'NA'}` | `NA` |  
|| `{'v': 'NULL'}` | `NULL` |
|| `{'v': 1}` | `1` |
|| `{'v': 'r:c(1,2,3)'}` | `c(1,2,3)` |
|| `{'v': 'plainstring'}` | `"plainstring"` |
  
- `Rvec`: Transform a python list to a R vector. For example:
  - `{{v | Rvec}}` with `{'v': [1,2,3]}` results in `c(1,2,3)`
- `Rlist`: Transform a python dict to a R list. For example:
  - `{{v | Rlist}}` with `{'v': {'a':1, 'b':2}}` results in `list(a=1, b=2)`
- `realpath`: Alias of `os.path.realpath`
- `readlink`: Alias of `os.readlink`
- `dirname`: Alias of `os.path.dirname`
- `basename`: Get the basename of a file. If a file is renamed by `PyPPL` in case of input files with the same basename, it tries to get the original basename. For example:

| Usage | Data | Result |
|-------|------|--------|
| `{{v `&#x7c;` basename}}` | `{'v': '/path/to/file.txt'}` | `file.txt` |
|| `{'v': '/path/to/file[1].txt'}` | `file.txt` |
| `{{v, orig `&#x7c;` basename}}` | `{'v': '/path/to/file[1].txt', 'orig': True}` | `file[1].txt`| 
  
- `bn`: Alias of `basename`
- `filename`: Similar as `basename` but without extension.
- `fn`: Alias of `filename`
- `filename2`: Get the filename without dot.
- `fn2`: Alias of `filename2`. (i.e: `/a/b/c.d.e.txt` -> `c`)
- `ext`: Get extension of a file. Alias of `os.path.splitext(x)[1]`
  - Dot is included. To remove the dot: `{{v | ext | [1:]}}`
- `prefix`: Get the prefix of a path, without extension. It acts like `{{v | dirname}}/{{v | filename}}`
- `prefix2`: Get the prefix of a path without dot in filename. (i.e: `/a/b/c.d.e.txt` -> `/a/b/c`)
- `quote`: Double-quote a string.
- `asquote`: Double quote items in a list and join them by space. For example:
  - `{{v | asquote}}` with `{'v': [1,2,3]}` results in `"1" "2" "3"`
- `acquote`: Double quote items in a list and join them by comma.
- `squote`: Single-quote a string.
- `json`: Dumps a python object to a json string. Alias of `json.dumps`
- `read`: Read the content of a file.
- `readlines`: Read the lines of a file. Empty lines are skipped by default. To return the empty lines for `{'v': '/path/to/file', 'skipEmptyLines': False}`: 
  - `{{v, skipEmptyLines | readlines}}`
- `repr`: Alias of python `repr` built-in function.

## Usage of built-in template engine
- Basic usage:

| Usage | Data | Result |
|-------|------|--------|
| `{{v}}`| `{'v': 1}`| `1` |
| `{{v.a}}`, `{{v['a']}}`| `{'v': {'a': 1}}`| `1` |
| `{{v.0}}`, `{{v[0]}}` | `{'v': [1]}` | `1` |
| `{{v.upper()}}` | `{'v': "a"}` | `A` |
| `{{v.0.upper()}}`| `{'v': ["a"]}` | `A` |
  
- Applying functions:

| Usage | Data | Result |
|-------|------|--------|
| `{{v `&#x7c;` R}}` | `{'v': True}` | `TRUE` |
| `{{v1, v2 `&#x7c;` paste}}` | `{'v1': 'Hello', 'v2': 'world!', 'paste': lambda x, y: x + ' ' + y}`| `Hello world!` |
| `{{v1, v2 `&#x7c;` lambda x, y: x + ' ' + y}}` | `{'v1': 'Hello', 'v2': 'world!'}`| `Hello world!` |

!!! note
    If you want to pass a literal value (for example: `1`, `True`), you CANNOT do this: `{{v, False | readlines}}`. Instead, you can either:

    * specify the value in data: `{'v': '/path/to/file', 'skipEmptyLines': False}`,   
    then `{{v, skipEmptyLines | readlines}}`; or
    * use `lambda` function: `{{v, readlines | lambda x, func: func(x, False)}}`
  
- `If-else/elif` statements:

| Usage | Data | Result |
|-------|------|--------|
| `{% if v %}`<br />&nbsp;&nbsp;`1`<br />`{% else %}`<br />&nbsp;&nbsp;`2`<br />`{% endif %}`|`{'v': True}`|`1`|
| `{% if v `&#x7c;` notExists %}`<br />&nbsp;&nbsp;`Path not exists.`<br />`{% elif v `&#x7c;` isDir %}`<br />&nbsp;&nbsp;`Path is a directory.`<br />`{% else %}`<br />&nbsp;&nbsp;`Path exists but is not a directory.`<br />`{% endif %}` | `{`<br />`'v': '/path/to/file', `<br />`'notExists': lambda x: not __import__('os').path.exists(x), `<br />`'isDir': __import__('os').path.isdir`<br />`}` |`Path exists but is not a directory.`|
  
- Loops:

| Usage | Data | Result |
|-------|------|--------|
|`{% for var in varlist %}{{var`&#x7c;`R}}{% endfor %}`|`{'varlist': ['abc', 'True', 1, False]}`|`"abc"TRUE1FALSE`|
|`{% for k , v in data.items() %}{{k}}:{{v}}{% endfor %}`|`{'data': {'a':1, 'b':2}}`|`a:1b:2`|
  
## Set environment of template engine
You can define you own functions/data for template rendering:
```python
pXXX.envs.data  = {'v1': 'a', 'v2': 'b', 'b': True}
pXXX.envs.os    = __import__('os')
pXXX.envs.paste = lambda x,y: x +' ' + y
# use them
pXXX.script = """
{% if data.b %}
print "{{data.v1, data.v2 | paste}}"
{% else %}
print "{{data.v1, data.v2 | os.path.join}}"
{% endif %}
"""
```
Then if `pXXX.envs.data['b'] is True`, it prints `a b`; otherwise it prints `a/b`.

## Use Jinja2
All the data and environment definition mentioned above are all applicable when you use `Jinja2` as your template engine.  
For usage of `Jinja2`, you may refer to its [official documentation][2].


[1]: https://github.com/aosabook/500lines/tree/master/template-engine
[2]: http://jinja.pocoo.org/