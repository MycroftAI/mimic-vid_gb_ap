project('mimic-vid_gb_ap', 'c', default_options : ['c_std=c99'], version: '0')
voice_name = 'vid_gb_ap'

build_plugin = get_option('plugin')

sources = [
  'src/vid_gb_ap.c',
  'src/vid_gb_ap_cg.c',
  'src/vid_gb_ap_cg_01_mcep_trees.c',
  'src/vid_gb_ap_cg_01_params.c',
  'src/vid_gb_ap_cg_08_durmodel.c',
  'src/vid_gb_ap_cg_10_durmodel.c',
  'src/vid_gb_ap_cg_12_mcep_trees.c',
  'src/vid_gb_ap_cg_12_params.c',
  'src/vid_gb_ap_cg_17_durmodel.c',
  'src/vid_gb_ap_cg_17_mcep_trees.c',
  'src/vid_gb_ap_cg_17_params.c',
  'src/vid_gb_ap_cg_f0_trees.c',
  'src/vid_gb_ap_cg_phonestate.c'
]

includes = [include_directories('include')]

# English Dependency:
mimicenglish = dependency('ttsmimic_english', required: false)
if mimicenglish.found()
  mimiccore = dependency('ttsmimic_core')
  mimic_plugin_dir = mimiccore.get_pkgconfig_variable('mimic_plugin_dir')
else
  # We compile it:
  mimic_english_sub = subproject('mimic-english')
  mimicenglish = mimic_english_sub.get_variable('ttsmimic_english_dep')
  mimiccore = mimic_english_sub.get_variable('mimiccore')
  mimic_plugin_dir = mimic_english_sub.get_variable('mimic_plugin_dir')
  includes += mimic_english_sub.get_variable('includes')
endif

message('Build mimic-@0@ plugin: @1@'.format(voice_name, build_plugin))


en_us_header_subdir = join_paths('ttsmimic', 'lang', 'en_US')

conf_data = configuration_data()
conf_data.set('ENABLE_@0@_BUILTIN'.format(voice_name.to_upper()), not build_plugin)

config_h = configure_file(output : 'mimic_@0@_config.h'.format(voice_name),
                          configuration : conf_data, install: true,
                          install_dir : join_paths(get_option('includedir'), en_us_header_subdir))

sources += [config_h]

voice_lib = library('ttsmimic_@0@'.format(voice_name), sources,
                    include_directories : includes,
                    dependencies: [mimicenglish, mimiccore],
                    install: true)

voice_dep = declare_dependency(link_with : voice_lib, 
                               include_directories : includes)


if build_plugin
  voice_plugin_lib = library(
    'ttsmimic_@0@_plugin'.format(voice_name),
    ['src/@0@_plugin.c'.format(voice_name), config_h],
    include_directories : includes,
    dependencies: [mimiccore, mimicenglish, voice_dep],
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
             subdirs: en_us_header_subdir,
             requires: ['ttsmimic_english', 'ttsmimic_core'],
             description: '@0@ support for text to speech synthesis in mimic.'.format(voice_name),
             version: meson.project_version())

