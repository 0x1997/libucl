project('ucl', 'c',
    default_options: [
        'c_std=c99',
    ]
)

# Basic feature checking for Linux
# Lua/libcurl/libfetch/OpenSSL support are disabled

compiler = meson.get_compiler('c')
conf_data = configuration_data()

code_using_atomic_built_in = '''
int main() {
    volatile unsigned long val = 1;
    __sync_synchronize();
    __sync_val_compare_and_swap(&val, 1, 0);
    __sync_add_and_fetch(&val, 1);
    __sync_sub_and_fetch(&val, 1);
    return 0;
}
'''

if compiler.compiles(code_using_atomic_built_in, name: 'gcc atomic intrinsics')
    conf_data.set('HAVE_ATOMIC_BUILTINS', 1)
endif

check_headers = [
    ['ctype.h', 'HAVE_CTYPE_H'],
    ['dlfcn.h', 'HAVE_DLFCN_H'],
    ['endian.h', 'HAVE_ENDIAN_H'],
    ['errno.h', 'HAVE_ERRNO_H'],
    ['fcntl.h', 'HAVE_FCNTL_H'],
    ['float.h', 'HAVE_FLOAT_H'],
    ['inttypes.h', 'HAVE_INTTYPES_H'],
    ['libgen.h', 'HAVE_LIBGEN_H'],
    ['limits.h', 'HAVE_LIMITS_H'],
    ['math.h', 'HAVE_MATH_H'],
    ['memory.h', 'HAVE_MEMORY_H'],
    ['regex.h', 'HAVE_REGEX_H'],
    ['stdarg.h', 'HAVE_STDARG_H'],
    ['stdbool.h', 'HAVE_STDBOOL_H'],
    ['stddef.h', 'HAVE_STDDEF_H'],
    ['stdint.h', 'HAVE_STDINT_H'],
    ['stdio.h', 'HAVE_STDIO_H'],
    ['stdlib.h', 'HAVE_STDLIB_H'],
    ['strings.h', 'HAVE_STRINGS_H'],
    ['string.h', 'HAVE_STRING_H'],
    ['sys/endian.h', 'HAVE_SYS_ENDIAN_H'],
    ['sys/mman.h', 'HAVE_SYS_MMAN_H'],
    ['sys/param.h', 'HAVE_SYS_PARAM_H'],
    ['sys/stat.h', 'HAVE_SYS_STAT_H'],
    ['sys/types.h', 'HAVE_SYS_TYPES_H'],
    ['unistd.h', 'HAVE_UNISTD_H'],
]

foreach i: check_headers
  if compiler.has_header(i.get(0))
    conf_data.set(i.get(1), 1)
  endif
endforeach

check_functions = [
    ['getpagesize', '#include <unistd.h>', 'HAVE_GETPAGESIZE'],
    ['mmap', '#include <sys/mman.h>', 'HAVE_MMAP'],
]

foreach i: check_functions
  if compiler.has_function(i.get(0), prefix: i.get(1))
    conf_data.set(i.get(2), 1)
  endif
endforeach

configure_file(
    input: 'config.h.meson',
    output: 'config.h',
    configuration: conf_data
)

inc = include_directories('include')

install_headers(
    'include/ucl.h',
    'include/ucl++.h',
)

lib = object_library('ucl',
    'src/ucl_hash.c',
    'src/ucl_parser.c',
    'src/ucl_util.c',
    'src/ucl_msgpack.c',
    'src/ucl_sexp.c',
    'src/ucl_emitter.c',
    'src/ucl_emitter_streamline.c',
    'src/ucl_schema.c',
    'src/ucl_emitter_utils.c',
    include_directories: [
        inc,
        include_directories('klib', 'src', 'uthash')
    ],
    c_args: [
        '-fPIC',
        '-DHAVE_CONFIG_H',
        '-W',
        '-Wall',
        '-Wno-pointer-sign',
        '-Wno-unused-function',
        '-Wno-unused-parameter',
        '-Wstrict-prototypes'
    ]
)
