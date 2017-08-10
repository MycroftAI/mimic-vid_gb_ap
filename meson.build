project('mimic-vid_gb_ap', 'c', default_options : ['c_std=c99'], version: '0')
voice_name = 'vid_gb_ap'

sources = [
  'src/vid_gb_ap_cg_17_mcep_trees.c',
  'src/vid_gb_ap_cg_01_mcep_trees.h',
  'src/vid_gb_ap_cg_f0_trees.c',
  'src/vid_gb_ap_cg_17_durmodel.h',
  'src/vid_gb_ap_cg_phonestate.c',
  'src/vid_gb_ap_cg_10_durmodel.h',
  'src/voxdefs.h',
  'src/vid_gb_ap_cg_12_params.c',
  'src/vid_gb_ap_cg.c',
  'src/vid_gb_ap_cg_12_mcep_trees.c',
  'src/vid_gb_ap_cg_01_mcep_trees.c',
  'src/vid_gb_ap_cg_17_durmodel.c',
  'src/vid_gb_ap_cg_f0_trees.h',
  'src/vid_gb_ap_cg_01_params.c',
  'src/vid_gb_ap_cg_17_params.c',
  'src/vid_gb_ap_cg_17_mcep_trees.h',
  'src/vid_gb_ap_cg_12_mcep_trees.h',
  'src/vid_gb_ap.c',
  'src/vid_gb_ap_cg_10_durmodel.c',
  'src/vid_gb_ap_cg_08_durmodel.h',
  'src/vid_gb_ap_cg_08_durmodel.c'
]

includes = [include_directories('include')]

# Core Dependency:
mimiccore = dependency('ttsmimiccore')
mimic_plugin_dir = mimiccore.get_pkgconfig_variable('mimic_plugin_dir')
mimicenglish = dependency('ttsmimicenglish')

# If mimic-core does not have plugin support, disable the plugin
if mimic_plugin_dir == ''
  build_plugin = false
else
  build_plugin = true
endif

en_us_header_subdir = join_paths('ttsmimic', 'lang', 'en_US')

conf_data = configuration_data()
conf_data.set('ENABLE_@0@_BUILTIN'.format(voice_name.to_upper()), not build_plugin)

config_h = configure_file(output : 'mimic_@0@_config.h'.format(voice_name),
                          configuration : conf_data, install: true,
                          install_dir : join_paths(get_option('includedir'), en_us_header_subdir))

sources += [mimic_english_config_h]

voice_lib = library('ttsmimic_@0@'.format(voice_name), sources,
                    include_directories : includes,
                    dependencies: [mimicenglish, mimiccore],
                    install: true)

voice_dep = declare_dependency(link_with : voice_lib, 
                               include_directories : includes)


if build_plugin
  voice_plugin_lib = library(
    'ttsmimic_@0@_plugin'.format(voice_name),
    ['src/@0@_plugin.c'.format(voice_name)],
    include_directories : includes,
    dependencies: [mimiccore, ttsmimiclib_english_dep],
    install_dir: mimic_plugin_dir,
    install: true)

  voice_plugin_dep = declare_dependency(
    link_with : voice_plugin_lib, 
    include_directories : includes)
endif



############ Headers #################

ttsmimic_headers = ['include/@0@.h'.format(voice_name)]

install_headers(ttsmimic_headers, subdir : en_us_header_subdir)


########### pkg-config #############

pkg = import('pkgconfig')

pkg.generate(libraries : voice_lib,
             name : 'ttsmimic_@0@'.format(voice_name),
             requires: ['ttsmimicenglish', 'ttsmimiccore'],
             description: '@0@ support for text to speech synthesis in mimic.'.format(voice_name),
             version: meson.project_version())

