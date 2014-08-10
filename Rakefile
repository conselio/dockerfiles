task :default => "minimum2scp:all"

namespace :minimum2scp do
  desc 'create all images'
  task :all => [:debian, :pb]

  desc 'build minimum2scp/debian image'
  task :debian do
    tag = ->(env, default){
      env ? (!env.empty? ? "TAG=#{env}" : "") : "TAG=#{default}"
    }[ENV['TAG_DEBIAN'], 'minimum2scp/debian:latest']
    env = ENV.select{|k,v| %w[http_proxy].include?(k)}.map{|k,v| "#{k}=#{v}"}.join(" ")
    sh "cd debian && rake mkimage #{tag} #{env}"
  end

  desc 'build minimum2scp/pb image'
  task :pb do
    tag = ->(env, default){
      env ? (!env.empty? ? "TAG=#{env}" : "") : "TAG=#{default}"
    }[ENV['TAG_PB'], 'minimum2scp/pb:latest']
    env = ENV.select{|k,v| %w[http_proxy].include?(k)}.map{|k,v| "#{k}=#{v}"}.join(" ")
    sh "cd pb && rake clean docker-build #{tag} #{env}"
  end
end
