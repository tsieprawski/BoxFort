project('boxfort', 'c',
		meson_version   : '>= 0.48.0',
		license         : 'MIT',
		version         : '0.1.0',
		default_options : ['c_std=c99', 'warning_level=2'])

config = configuration_data()

config.set('BXF_MANGLING', 'none')
if host_machine.cpu_family() == 'x86'
	config.set('BXF_ARCH', 'i386')
	config.set('BXF_ABI', 'cdecl')
	config.set('BXF_BITS', 32)
	config.set('BXF_ARCH_x86', 1)
	config.set('BXF_MANGLING', 'leading-underscore')
elif host_machine.cpu_family() == 'x86_64'
	config.set('BXF_ARCH', 'x86_64')
	if host_machine.system() == 'windows'
		config.set('BXF_ABI', 'win')
	else
		config.set('BXF_ABI', 'systemv')
	endif
	config.set('BXF_BITS', 64)
	config.set('BXF_ARCH_x86_64', 1)
elif host_machine.cpu_family() == 'arm'
	config.set('BXF_ARCH', 'arm')
	config.set('BXF_ABI', 'arm')
	config.set('BXF_BITS', 32)
	config.set('BXF_ARCH_ARM', 1)
elif host_machine.cpu_family() == 'aarch64'
	config.set('BXF_ARCH', 'arm64')
	config.set('BXF_ABI', 'arm64')
	config.set('BXF_BITS', 64)
	config.set('BXF_ARCH_ARM64', 1)
else
    error('Architecture @0@ is not supported.'.format(host_machine.cpu_family()))
endif

config.set('BXF_EXE_FORMAT', 'elf')
config.set('BXF_OS_FAMILY', 'posix')

if host_machine.system() == 'windows'
	config.set('BXF_EXE_FORMAT', 'pe')
	config.set('BXF_OS_FAMILY', 'windows')
elif host_machine.system() == 'darwin'
	config.set('BXF_EXE_FORMAT', 'mach-o')
endif

config.set('BXF_EXE_FMT_' + config.get('BXF_EXE_FORMAT').to_upper(), 1)

cc = meson.get_compiler('c')

add_project_arguments(
	cc.get_supported_arguments([
		'-Wno-unused-parameter',
		'-Wno-unused-value',
		'-fvisibility=hidden',

		# MSVC-specific stuff
		'/SAFESEH:NO',
		'/source-charset:utf-8',
	]),
	'-D_GNU_SOURCE',
	language: 'c')

checks = [
	{'fn': 'clock_gettime'},
	{'fn': 'gettimeofday'},

	{'sym': 'CLOCK_MONOTONIC_RAW',                'header': 'time.h'},
	{'sym': 'PR_SET_PDEATHSIG',                   'header': 'sys/prctl.h'},
	{'sym': '_DYNAMIC',                           'header': 'link.h'},
	{'sym': '_r_debug',                           'header': 'link.h'},
	{'sym': 'environ',                            'header': 'unistd.h'},
	{'sym': 'pthread_cond_timedwait_relative_np', 'header': 'pthread.h'},
]

check_prelude = '''
#define _GNU_SOURCE
'''

foreach check : checks
	chk_prefix = check.get('prefix', '')
	if chk_prefix != ''
		chk_prefix = chk_prefix + '_'
	endif

	result = false
	if check.has_key('fn')
		name = check.get('fn')
		result = cc.has_function(name, prefix: check_prelude)
	elif check.has_key('sym')
		name = check.get('sym')
		result = cc.has_header_symbol(check.get('header'), name, prefix: check_prelude)
	endif

	name = 'HAVE_@1@@0@'.format(name.to_upper(), chk_prefix.to_upper())
	config.set(name, result)
endforeach

api = include_directories('include')

threads = dependency('threads')
librt = cc.find_library('rt', required: false)

install_headers('include/boxfort.h')

subdir('src')
