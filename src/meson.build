sources = [
	'asm/trampoline-@0@.S'.format(config.get('BXF_ARCH')),
	'addr.c',
	'arena.c',
	'context.c',
	'exe-@0@.c'.format(config.get('BXF_EXE_FORMAT')),
	'sandbox-@0@.c'.format(config.get('BXF_OS_FAMILY')),
	'sandbox.c',
	'timeout-@0@.c'.format(config.get('BXF_OS_FAMILY')),
	'timestamp.c',
]

configure_file(configuration : config,
		input  : 'config.h.in',
		output : 'config.h')

static_library('boxfort', sources,
	include_directories: [api],
	dependencies: [librt, threads],
	install: true,
)
