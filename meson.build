project('openSeaChest', 'c', license: 'MPL-2.0', version: '2.0.2')

c = meson.get_compiler('c')

if not get_option('tcg').enabled()
  add_project_arguments('-DDISABLE_TCG_SUPPORT', language : 'c')
endif

if get_option('debug')
  add_project_arguments('-D_DEBUG', language : 'c')
endif

if get_option('libc_musl')
  add_project_arguments('-DUSING_MUSL_LIBC=1', language : 'c')
endif

exec_prefix = 'openSeaChest_'
common_sources = ['src/EULA.c', 'src/openseachest_util_options.c']

os_deps = []

if target_machine.system() == 'windows'
  if c.get_define('__MINGW32__') == ''
    cmake = import('cmake')

    wingetopt = cmake.subproject('wingetopt')
    wingetopt_dep = wingetopt.dependency('wingetopt')
    os_deps += [wingetopt_dep]
  endif
  add_project_arguments('-D_CRT_SECURE_NO_WARNINGS', language : 'c')
endif

opensea_common = subproject('opensea-common')
opensea_common_dep = opensea_common.get_variable('opensea_common_dep')

opensea_transport = subproject('opensea-transport')
opensea_transport_dep = opensea_transport.get_variable('opensea_transport_dep')

opensea_operations = subproject('opensea-operations')
opensea_operations_dep = opensea_operations.get_variable('opensea_operations_dep')

incdir = include_directories('include')

exe_src_map = {
  'FormatUnit': 'Format'
}

foreach p : get_option('tools')
  executable('openSeaChest_' + p, common_sources, 'utils/C/openSeaChest/openSeaChest_' + exe_src_map.get(p, p) + '.c', dependencies : [opensea_common_dep, opensea_transport_dep, opensea_operations_dep, os_deps], include_directories : incdir, install : true)
  if p not in ['NVMe', 'PassthroughTest', 'Security', 'ZBD'] # Blacklist tools without man pages
    install_man('docs/man/man8/openSeaChest_' + exe_src_map.get(p, p) + '.8')
  endif
endforeach
install_man('docs/man/man8/openSeaChest.8')
