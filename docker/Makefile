default:
	@echo ""
	@echo "Targets:"
	@echo ""
	@echo "  params                 Print build parameter"
	@echo "  build                  Build images"
	@echo "  version                Print version of images"
	@echo "  test                   Test images"
	@echo "  publish                Publish images"
	@echo ""


params:
	@echo "${CROSSBAR_VERSION}"
	@echo "${CROSSBAR_VCS_REF}"
	@echo "${BUILD_DATE}"

build: build_cpy3 build_pypy3

version: version_cpy3 version_pypy3

test: test_cpy3 test_pypy3

download_wheels:
	mkdir -p ./.wheels/
	rm -f ./.wheels/*.whl
	curl -o ./.wheels/txaio-latest-py2.py3-none-any.whl       https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/txaio-latest-py2.py3-none-any.whl
	curl -o ./.wheels/autobahn-latest-py2.py3-none-any.whl    https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/autobahn-latest-py2.py3-none-any.whl
	curl -o ./.wheels/zlmdb-latest-py2.py3-none-any.whl       https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/zlmdb-latest-py2.py3-none-any.whl

prepare:
	-rm -rf ./node
	crossbar init --appdir ./node
	mkdir node/web/js -p

publish_amd64_cpy3:
	# amd64 / community / cpy3
	docker push crossbario/crossbar:cpy-amd64
	docker push crossbario/crossbar:cpy-amd64-${CROSSBAR_VERSION}

publish_amd64_pypy3:
	# amd64 / community / pypy3
	docker push crossbario/crossbar:pypy-amd64
	docker push crossbario/crossbar:pypy-amd64-${CROSSBAR_VERSION}

publish_armhf_cpy3:
	# armhf / community / cpy3
	docker push crossbario/crossbar:cpy-armhf
	docker push crossbario/crossbar:cpy-armhf-${CROSSBAR_VERSION}

	# armhf / community / pypy3
	# docker push crossbario/crossbar-armhf:pypy3
	# docker push crossbario/crossbar-armhf:pypy3-${CROSSBAR_VERSION}

publish_aarch64_cpy3:
	# aarch64 / community / cpy3
	docker push crossbario/crossbar:cpy-arm64v8
	docker push crossbario/crossbar:cpy-arm64v8-${CROSSBAR_VERSION}

publish_aarch64_pypy3:
	# aarch64 / community / pypy3
	docker push crossbario/crossbar:pypy-arm64v8
	docker push crossbario/crossbar:pypy-arm64v8-${CROSSBAR_VERSION}

trigger_microbadger:
	# trigger updates of metainfo on https://microbadger.com/
	curl -X POST https://hooks.microbadger.com/images/crossbario/crossbar/jA-pM9fXwd6v4Q7M3PWGG10XPAE=
	curl -X POST https://hooks.microbadger.com/images/crossbario/crossbar-armhf/3hB5Fn-4Bf2VV9ihB8e7v687Y60=
	curl -X POST https://hooks.microbadger.com/images/crossbario/crossbar-aarch64/ekMu3620yWLtfpxIqJQgPSmDNYE=


requirements:
	sudo apt-get update
	sudo apt-get install -y qemu qemu-kvm qemu-system qemu-system-x86 qemu-system-arm
	sudo apt-get install -y --no-install-recommends qemu-user-static binfmt-support
	sudo update-binfmts --enable qemu-arm
	sudo update-binfmts --enable qemu-aarch64
	sudo update-binfmts --display qemu-arm
	sudo update-binfmts --display qemu-aarch64

# Install Qemu (this is needed for cross-building armhf/aarch64 on amd64)
qemu_deps:
	mkdir -p ./.qemu
	cp /usr/bin/qemu-system-aarch64 ./.qemu
	cp /usr/bin/qemu-system-arm ./.qemu

#
# Copy over current AutobahnJS into the default node directory
# we bake into the images
#
autobahn:
	mkdir -p ./.node/web/js/
	cp ../../autobahn-js-browser/autobahn.* ./.node/web/js/
	cp ../../autobahn-js-browser/CHECKSUM.* ./.node/web/js/
	cp ../../autobahn-js-browser/LICENSE ./.node/web/js/

#
# List all images
#
list:
	-docker images crossbario/crossbar*

#
# Remove all images !!!
#
clean:
	./removeall.sh


download_wheels:
	mkdir -p ./.wheels/
	rm -f ./.wheels/*.whl
	curl -o ./.wheels/txaio-latest-py2.py3-none-any.whl       https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/txaio-latest-py2.py3-none-any.whl
	curl -o ./.wheels/autobahn-latest-py2.py3-none-any.whl    https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/autobahn-latest-py2.py3-none-any.whl
	curl -o ./.wheels/zlmdb-latest-py2.py3-none-any.whl       https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/zlmdb-latest-py2.py3-none-any.whl
	curl -o ./.wheels/crossbar-latest-py2.py3-none-any.whl    https://crossbarbuilder.s3.eu-central-1.amazonaws.com/wheels/crossbar-latest-py2.py3-none-any.whl

#
# BUILD: Community / CPython 3
#
build_cpy3: build_amd64_cpy3 build_armhf_cpy3 build_aarch64_cpy3

build_amd64_cpy3:
	# enforce auto-generating a new key when using the images
	rm -f node/.crossbar/key.*
	time docker build \
		--build-arg CROSSBAR_ARCH=amd64 \
		--build-arg CROSSBAR_BUILD_DATE=${CROSSBAR_BUILD_DATE} \
		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
		-t crossbario/crossbar:cpy-amd64 \
		-t crossbario/crossbar:cpy-amd64-${CROSSBAR_VERSION} \
		-f Dockerfile.cpy-slim \
		.
	docker images crossbario/crossbar:cpy-amd64

build_aarch64_cpy3:
	# enforce auto-generating a new key when using the images
	rm -f node/.crossbar/key.*
	docker build \
		--build-arg CROSSBAR_ARCH=arm64v8 \
		--build-arg BUILD_DATE=${CROSSBAR_BUILD_DATE} \
		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
		-t crossbario/crossbar:cpy-arm64v8 \
		-t crossbario/crossbar:cpy-arm64v8-${CROSSBAR_VERSION} \
		-f ./Dockerfile.cpy-slim .
	docker images crossbario/crossbar:cpy-arm64v8

#build_amd64_cpy3:
#	docker build \
#		--build-arg BUILD_DATE=${BUILD_DATE} \
#		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
#		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
#		-t crossbario/crossbar:latest \
#		-t crossbario/crossbar:cpy3 \
#		-t crossbario/crossbar:cpy3-${CROSSBAR_VERSION} \
#		-f ./x86_64/Dockerfile.cpy3 .

build_armhf_cpy3:
	# enforce auto-generating a new key when using the images
	rm -f node/.crossbar/key.*
	time docker build \
		--build-arg CROSSBAR_ARCH=armhf \
		--build-arg CROSSBAR_BUILD_DATE=${CROSSBAR_BUILD_DATE} \
		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
		-t crossbario/crossbar:cpy-armhf \
		-t crossbario/crossbar:cpy-armhf-${CROSSBAR_VERSION} \
		-f Dockerfile.cpy-slim \
		.
	docker images crossbario/crossbar:cpy-armhf

# build_aarch64_cpy3:
# 	# enforce auto-generating a new key when using the images
# 	rm -f node/.crossbar/key.*
# 	docker build \
# 		--build-arg BUILD_DATE=${BUILD_DATE} \
# 		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
# 		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
# 		-t crossbario/crossbar-aarch64:latest \
# 		-t crossbario/crossbar-aarch64:cpy3 \
# 		-t crossbario/crossbar-aarch64:cpy3-${CROSSBAR_VERSION} \
# 		-f ./aarch64/Dockerfile.cpy3 .


#
# BUILD: Community / PyPy 3
#
build_pypy3: build_amd64_pypy3 build_armhf_pypy3 build_aarch64_pypy3

build_amd64_pypy3:
	# enforce auto-generating a new key when using the images
	rm -f node/.crossbar/key.*
	time docker build \
		--build-arg CROSSBAR_ARCH=amd64 \
		--build-arg CROSSBAR_BUILD_DATE=${CROSSBAR_BUILD_DATE} \
		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
		-t crossbario/crossbar:pypy-amd64 \
		-t crossbario/crossbar:pypy-amd64-${CROSSBAR_VERSION} \
		-f Dockerfile.pypy-slim \
		.
	docker images crossbario/crossbar:pypy-amd64

build_aarch64_pypy3:
	# enforce auto-generating a new key when using the images
	rm -f node/.crossbar/key.*
	docker build \
		--build-arg CROSSBAR_ARCH=arm64v8 \
		--build-arg BUILD_DATE=${CROSSBAR_BUILD_DATE} \
		--build-arg CROSSBAR_VCS_REF=${CROSSBAR_VCS_REF} \
		--build-arg CROSSBAR_VERSION=${CROSSBAR_VERSION} \
		-t crossbario/crossbar:pypy-arm64v8 \
		-t crossbario/crossbar:pypy-arm64v8-${CROSSBAR_VERSION} \
		-f ./Dockerfile.pypy-slim .
	docker images crossbario/crossbar:pypy-arm64v8

build_armhf_pypy3:
	@echo "PyPy3 isn't available for armhf yet"


#
# VERSION: Community / CPython 3
#
version_cpy3: version_amd64_cpy3 version_armhf_cpy3 version_aarch64_cpy3

version_amd64_cpy3:
	docker run \
		--rm --entrypoint=/usr/local/bin/crossbar -it \
		crossbario/crossbar:cpy-amd64-${CROSSBAR_VERSION} version

version_armhf_cpy3:
	docker run \
		--rm --entrypoint=/usr/local/bin/crossbar -it \
		crossbario/crossbar:cpy-armhf-${CROSSBAR_VERSION} version

version_aarch64_cpy3:
	docker run \
		--rm --entrypoint=/usr/local/bin/crossbar -it \
		crossbario/crossbar:cpy-arm64v8-${CROSSBAR_VERSION} version


#
# VERSION: Community / PyPy 3
#
version_pypy3: version_amd64_pypy3 version_armhf_pypy3 version_aarch64_pypy3

version_amd64_pypy3:
	docker run \
		--rm --entrypoint=/usr/local/bin/crossbar -it \
		crossbario/crossbar:pypy-amd64-${CROSSBAR_VERSION} version

version_armhf_pypy3:
	@echo "PyPy3 isn't available for armhf yet"

version_aarch64_pypy3:
	docker run \
		--rm --entrypoint=/usr/local/bin/crossbar -it \
		crossbario/crossbar:pypy-arm64v8-${CROSSBAR_VERSION} version


#
# TEST: Community / CPython 3
#
test_cpy3: test_amd64_cpy3 test_armhf_cpy3 test_aarch64_cpy3

test_amd64_cpy3:
	docker run \
		--rm -it -p 8080:8080 --name crossbar \
		crossbario/crossbar:cpy-amd64-${CROSSBAR_VERSION}

test_armhf_cpy3:
	docker run \
		--rm -it -p 8080:8080 --name crossbar \
		crossbario/crossbar:cpy-armhf-${CROSSBAR_VERSION}

test_aarch64_cpy3:
	docker run \
		--rm -it -p 8080:8080 --name crossbar \
		crossbario/crossbar:cpy-arm64v8-${CROSSBAR_VERSION}


#
# TEST: Community / PyPy 3
#
test_pypy3: test_amd64_pypy3 test_armhf_pypy3 test_aarch64_pypy3

test_amd64_pypy3:
	docker run \
		--rm -it -p 8080:8080 --name crossbar \
		crossbario/crossbar:pypy-amd64-${CROSSBAR_VERSION}

test_armhf_pypy3:
	@echo "PyPy3 isn't available for armhf yet"

test_aarch64_pypy3:
	docker run \
		--rm -it -p 8080:8080 --name crossbar \
		crossbario/crossbar:pypy-arm64v8-${CROSSBAR_VERSION}
