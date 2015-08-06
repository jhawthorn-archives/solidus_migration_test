
class Gemfile
  def initialize(spree_or_solidus, spree_version, rails_version)
    @spree_or_solidus = spree_or_solidus
    @rails_version = rails_version
    @spree_version = spree_version
  end

  def to_s
    <<-EOS
source 'https://rubygems.org'
gem 'rails', '#@rails_version'
gem 'sqlite3'
gem '#@spree_or_solidus', '#@spree_version'
EOS
  end
end

SPREE_2_2   = Gemfile.new('spree', '~> 2.2.0', '~> 4.0.0')
SPREE_2_3   = Gemfile.new('spree', '~> 2.3.0', '~> 4.1.8')
SPREE_2_4   = Gemfile.new('spree', '~> 2.4.0', '~> 4.1.8')
SPREE_3_0   = Gemfile.new('spree', '~> 3.0.0', '~> 4.2.0')
SOLIDUS_1_0 = Gemfile.new('solidus', '~> 1.0.0.rc1', '~> 4.2.0')

def assert_upgrade(from, to)
  rm_rf 'dummy'
  mkdir 'dummy'
  Dir.chdir 'dummy' do
    File.write("Gemfile", from.to_s)
    sh 'bundle install --path=../vendor/bundle --quiet'
    sh 'bundle exec rails new . --skip-bundle --force --skip-gemfile --quiet'
    sh 'bundle exec rake db:drop db:create'
    sh 'bundle exec rails g spree:install --auto-accept'

    # Perform upgrade
    File.write("Gemfile", to.to_s)
    sh 'bundle update'
    sh 'bundle exec rake railties:install:migrations'
    sh 'bundle exec rake db:migrate'

    # Throw some sanity checks into assertions.rb
    sh 'bundle exec rails runner ../assertions.rb'
  end
end

task default: :test_all

# skipping test_3_0 for now, which doesn't work yet
task test_all: [:test_2_2, :test_2_3, :test_2_4]

task :test_2_2 do
  assert_upgrade(SPREE_2_2, SOLIDUS_1_0)
end

task :test_2_3 do
  assert_upgrade(SPREE_2_3, SOLIDUS_1_0)
end

task :test_2_4 do
  assert_upgrade(SPREE_2_4, SOLIDUS_1_0)
end

task :test_3_0 do
  assert_upgrade(SPREE_3_0, SOLIDUS_1_0)
end

