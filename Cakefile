fs   = require "fs"
FFI  = require "node-ffi"
libc = new FFI.Library(null, "system": ["int32", ["string"]])
run  = libc.system
pkg  = require "./package"

VERSION = pkg.version
PACKAGE = "annotator.heatmap"
OUTPUT  = "pkg/#{PACKAGE}.min.js"
DEV_OUTPUT = "pkg/#{PACKAGE}.dev.js"
STYLES  = "pkg/#{PACKAGE}.css"
COFFEE  = "`npm bin`/coffee"
UGLIFY  = "`npm bin`/uglifyjs"
HEADER  = """
/*  Heatmap Annotator Plugin - v#{VERSION}
 *  Copyright 2012, Hypothesis <hypothes.is>
 *  Dual licensed under the MIT license & GPLv3
 *  More Information: http://github.com/FinalsClub/annotator-heatmap-plugin
 */
"""

FILE_LIST = [
    'src/heatmap.coffee'
]

task "watch", "Watch the coffee directories and output to ./lib", ->
  run "#{COFFEE} --watch --output ./lib ./src"

task "build", "Concatenates and minifies CSS & JS", (options) ->
  MINIFY = UGLIFY

  run """
  mkdir -p pkg && echo "#{HEADER}" > #{OUTPUT} &&
  cat #{FILE_LIST} |
  #{COFFEE} --stdio --print | #{MINIFY} >> #{OUTPUT} && echo "" >> #{OUTPUT}
  """
  utils.inline ["css/annotator.heatmap.css"], STYLES

task "dev-build", "Concatenates and compiles coffeescript with no minification", (options) ->
  MINIFY = "cat"

  run """
  mkdir -p pkg && echo "#{HEADER}" > #{DEV_OUTPUT} &&
  cat #{FILE_LIST} |
  #{COFFEE} --stdio --print >> #{DEV_OUTPUT} && echo "" >> #{DEV_OUTPUT}
  """
  utils.inline ["css/annotator.heatmap.css"], STYLES

utils =
  dataurlify: (css) ->
    # NB: path to image is "src/..." because the CSS urls start with "../img"
    b64_str = (name) -> fs.readFileSync("src/#{name}.png").toString('base64')
    b64_url = (m...) -> "url('data:image/png;base64,#{b64_str(m[2])}')"
    css.replace(/(url\(([^)]+)\.png\))/g, b64_url)

  inline: (src, dest) ->
    run "echo '#{HEADER}' > #{dest}"
    run "cat #{src.join(' ')} >> #{dest}"
    code = fs.readFileSync(dest, 'utf8')
    fs.writeFileSync(dest, @dataurlify(code))
