# Kicad Component Layout Plugin

A python plugin for KiCad to assist with script driven component layout.

## Note for this fork.

This ([ikeji/kicad_component_layout](https://github.com/ikeji/kicad_component_layout)) is a fork of [devbisme's original repository](https://github.com/devbisme/kicad_component_layout).

This fork contains these changes:

- Support latest KiCAD (tested at 7.0.8)
- Use python's dict literal instead of yaml (to avoid dependency to pyyaml).

## How to install

The `component_layout_plugin.py` file needs to be located into the KiCad script search path. For
example, on linux, it could go in `~/.kicad/scripting`. In kicad 6.0.1 (and later?) you can go to
`Tools->External Plugins->Open Plugins Directory` to find the location.
Once there, the script should be available to run in KiCad under 'Tools -> External Pluging', or
using the button on the toolbar.

## How to use

When run in pcbnew, the plugin reads information about how to position components on the board from
the selected file. The data in the file allows the plugin
to change the layer (top/bottom), position, rotation, and footprint for the given module.

An example layout.py file:

```
    {'origin': [100,100], # Offset applied to all component locations
     'components': {
       'R1': {
         'location': [10, 10], # x,y mm
         'rotation': 180.0, # r degrees (float)
         'flip': False,
         'footprint': {
           'path': 'path/to/library.pretty',
           'name': 'SomeFootPrint'
         }
       },
       'J1':
         ...
```

All the fields are optional for each component (e.g. leave footprint unspecified
to leave the footprint unchanged).

The footprint path is relative to the directory containing the KiCad PCB file.

Typically, I will then create a python script to generate the layout.py based on the needs of
the design.

The components must be instantiated in the schematic first.

## Caveats

1. The schematic has to assign a footprint, so if you override the footprint in
layout.py, the plugin will have to be run again after importing changes from
schematic in order to restore the footprint.
2. You have to check the "Re-associate footprints by refdes" option when performing
the update from schematic. If you don't, it will delete and recreate all the
components with the footprints in the schematic. You can simply run the plugin again to fix them.
3. This works with KiCad 5.1.9, and with KiCad 6.0.4 -- see below for 6.0.1, and 6.0.2. It may stop working on a future version.
4. Make sure you do not have any components selected when you run the layout plugin.
If your layout file changes footprints while components are selected, this causes KiCad
to crash -- this appears to no longer be an issue in KiCad 6.x.

## Known issue with 6.0.1, 6.0.2

This script is updated to work with KiCad 6.0.1. There were some changes, and
if you have an old version it will not work -- likely you get will an error
about missing the 'FindModuleByReference` function. However, there is an unresolved
issue that causes KiCad to segfault on exit after the script is run. This doesn't
prevent the script from working, and as far as I can tell, KiCad continues to run
correctly after running the script until you exit.
See [this issue](https://gitlab.com/kicad/code/kicad/-/issues/10951).

This was resolved in 6.0.3. 
