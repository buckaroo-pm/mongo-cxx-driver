load('//:subdir_glob.bzl', 'subdir_glob')
load('//:buckaroo_macros.bzl', 'buckaroo_cell')

mongo_c_driver = \
  buckaroo_cell('github.com/buckaroo-pm/mongo-c-driver')

genrule(
  name = 'configure',
  out = 'out',
  srcs = glob([
    '*.txt',
    'etc/*',
    'cmake/**/*.cmake',
    'cmake/**/*.txt',
    'src/**/*.txt',
    'src/**/*.in',
  ]),
  cmd = ' && '.join([
    'mkdir $OUT',
    'cd $OUT',
    'cmake $SRCDIR',
  ]),
  cacheable = False,
)

genrule(
  name = 'bsoncxx-config',
  out = 'config.hpp',
  srcs = [
    'src/bsoncxx/config/config.hpp.in',
  ],
  cmd = ' && '.join([
    'cp $SRCS $OUT',
    'sed -i "s/#cmakedefine BSONCXX_POLY_USE_STD_EXPERIMENTAL/#undef BSONCXX_POLY_USE_STD_EXPERIMENTAL/g" $OUT',
    'sed -i "s/#cmakedefine BSONCXX_POLY_USE_MNMLSTC/#undef BSONCXX_POLY_USE_MNMLSTC/g" $OUT',
    'sed -i "s/#cmakedefine BSONCXX_POLY_USE_SYSTEM_MNMLSTC/#undef BSONCXX_POLY_USE_SYSTEM_MNMLSTC/g" $OUT',
    'sed -i "s/#cmakedefine BSONCXX_POLY_USE_BOOST/#undef BSONCXX_POLY_USE_BOOST/g" $OUT',
    'sed -i "s/#cmakedefine BSONCXX_POLY_USE_STD/#define BSONCXX_POLY_USE_STD 1/g" $OUT',
    'sed -i "s/@BSONCXX_INLINE_NAMESPACE@/v_noabi/g" $OUT',
  ]),
)

genrule(
  name = 'bsoncxx-version',
  out = 'version.hpp',
  srcs = [
    'src/bsoncxx/config/version.hpp.in',
  ],
  cmd = ' && '.join([
    'cp $SRCS $OUT',
    'sed -i "s/@BSONCXX_VERSION_MAJOR@/0/g" $OUT',
    'sed -i "s/@BSONCXX_VERSION_MINOR@/0/g" $OUT',
    'sed -i "s/@BSONCXX_VERSION_PATCH@/0/g" $OUT',
    'sed -i "s/@BSONCXX_VERSION_EXTRA@/0/g" $OUT',
  ]),
)

genrule(
  name = 'bsoncxx-export',
  out = 'export.hpp',
  cmd = 'touch $OUT',
)

genrule(
  name = 'bsoncxx-private-config',
  out = 'config.hh',
  srcs = [
    'src/bsoncxx/config/private/config.hh.in',
  ],
  cmd = 'cp $SRCS $OUT',
)

cxx_library(
  name = 'bsoncxx',
  header_namespace = '',
  exported_headers = dict(
    subdir_glob([
      ('src', 'bsoncxx/**/*.hpp'),
      ('src', 'bsoncxx/**/*.hh'),
    ]).items() + [
      ('bsoncxx/config/version.hpp', ':bsoncxx-version'),
      ('bsoncxx/config/config.hpp', ':bsoncxx-config'),
      ('bsoncxx/config/private/config.hh', ':bsoncxx-private-config'),
      ('bsoncxx/config/export.hpp', 'src/bsoncxx/config/export.linux.hpp'),
    ]
  ),
  srcs = glob([
    'src/bsoncxx/**/*.cpp',
  ], exclude = glob([
    'src/bsoncxx/test/**/*.cpp',
  ])),
  deps = [
    mongo_c_driver + '//:bson',
  ],
  visibility = [
    'PUBLIC',
  ],
)

genrule(
  name = 'mongocxx-config',
  out = 'config.hpp',
  srcs = [
    'src/mongocxx/config/config.hpp.in',
  ],
  cmd = ' && '.join([
    'cp $SRCS $OUT',
    'sed -i "s/@MONGOCXX_INLINE_NAMESPACE@/v_noabi/g" $OUT',
  ]),
)

genrule(
  name = 'mongocxx-version',
  out = 'version.hpp',
  srcs = [
    'src/mongocxx/config/version.hpp.in',
  ],
  cmd = ' && '.join([
    'cp $SRCS $OUT',
    'sed -i "s/@MONGOCXX_VERSION_MAJOR@/0/g" $OUT',
    'sed -i "s/@MONGOCXX_VERSION_MINOR@/0/g" $OUT',
    'sed -i "s/@MONGOCXX_VERSION_PATCH@/0/g" $OUT',
    'sed -i "s/@MONGOCXX_VERSION_EXTRA@/0/g" $OUT',
  ]),
)

genrule(
  name = 'mongocxx-private-config',
  out = 'config.hh',
  srcs = [
    'src/mongocxx/config/private/config.hh.in',
  ],
  cmd = ' && '.join([
    'cp $SRCS $OUT',
    'sed -i "s/#cmakedefine MONGOCXX_ENABLE_SSL/#undef MONGOCXX_ENABLE_SSL/g" $OUT',
  ]),
)

cxx_library(
  name = 'mongocxx',
  header_namespace = '',
  exported_headers = dict(
    subdir_glob([
      ('src', 'mongocxx/**/*.hpp'),
      ('src', 'mongocxx/**/*.hh'),
    ]).items() + [
      ('mongocxx/config/config.hpp', ':mongocxx-config'),
      ('mongocxx/config/private/config.hh', ':mongocxx-private-config'),
      ('mongocxx/config/version.hpp', ':mongocxx-version'),
      ('mongocxx/config/export.hpp', 'src/mongocxx/config/export.linux.hpp'),
    ]
  ),
  srcs = glob([
    'src/mongocxx/**/*.cpp',
  ], exclude = glob([
    'src/mongocxx/test/**/*.cpp',
    'src/mongocxx/test_util/**/*.cpp',
  ])),
  deps = [
    ':bsoncxx',
    mongo_c_driver + '//:mongoc',
  ],
  visibility = [
    'PUBLIC',
  ],
)
