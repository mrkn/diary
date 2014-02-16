require 'fileutils'
require 'pathname'

REPO_URI =
  if ENV.has_key? 'GH_TOKEN'
    'https://$GH_TOKEN@github.com/mrkn/diary'
  else
    'git@github.com:mrkn/diary.git'
  end
BRANCH = 'gh-pages'
DEST_DIR = 'build'

def init_repo(uri, branch)
  unless Pathname("#{DEST_DIR}/.git").directory?
    FileUtils.rm_rf DEST_DIR
    system "git clone --quiet #{uri} #{DEST_DIR} 2>/dev/null"
  end

  Dir.chdir(DEST_DIR) do
    sh "git checkout --orphan #{branch}"
  end
end

def update_repo(branch)
  Dir.chdir(DEST_DIR) do
    sh 'git fetch origin'
    sh "git reset --hard origin/#{branch}"
  end
end

def build
  sh 'bundle exec middleman build'
  sh "cp CNAME #{DEST_DIR}/"
end

def clean
  sh "rm -rf #{DEST_DIR}/*"
end

def push_repo(uri, branch)
  sha1, _ = `git log -n 1 --oneline`.strip.split(' ', 2)

  Dir.chdir(DEST_DIR) do
    sh 'git add -A'
    sh "git commit -m 'Update with #{sha1}'"
    system "git push --quiet #{uri} #{branch}"
  end
end

desc 'Setup build'
task :setup do
  init_repo REPO_URI, BRANCH
  update_repo BRANCH
end

desc 'Build site'
task :build do
  clean
  build
end

desc 'Clean up built files'
task :clean do
  clean
end

desc 'Publish site'
task :publish do
  push_repo REPO_URI, BRANCH
end
